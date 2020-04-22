---
title: Beoordelingen in Azure Migrate Server Assessment
description: Meer informatie over beoordelingen in Azure Migrate Server Assessment
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 2f76ea5f195be2914cdcdb4de9e93af38504d66e
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769924"
---
# <a name="assessments-in-azure-migrate-server-assessment"></a>Beoordelingen in Azure Migreren: Serverbeoordeling

In dit artikel vindt u een overzicht van beoordelingen in het hulpprogramma [Azure Migrate: Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) De tool kan on-premises Virtuele VMware-machines, Hyper-V VM's en fysieke servers beoordelen op migratie naar Azure.

## <a name="whats-an-assessment"></a>Wat is een beoordeling?

Een beoordeling met het hulpprogramma Serverbeoordeling meet de gereedheid en schat het effect van het migreren van on-premises servers naar Azure.

> [!NOTE]
> Bekijk in Azure Government de [ondersteunde doelbeoordelingslocaties.](migrate-support-matrix.md#supported-geographies-azure-government) Houd er rekening mee dat vm-grootteaanbevelingen in beoordelingen de VM-serie specifiek voor Government Cloud-regio's zullen gebruiken. [Meer informatie](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) over VM-typen.

## <a name="types-of-assessments"></a>Soorten beoordelingen

Beoordelingen die u maakt met ServerAssessment zijn een point-in-time momentopname van gegevens. Server Assessment biedt twee soorten beoordelingen.

**Beoordelingstype** | **Details** | **Gegevens**
--- | --- | ---
**Op prestaties gebaseerd** | Beoordelingen die aanbevelingen doen op basis van verzamelde prestatiegegevens | De aanbeveling voor VM-grootte is gebaseerd op cpu- en RAM-gebruiksgegevens.<br/><br/> De aanbeveling voor het type schijf is gebaseerd op de invoer-/uitvoerbewerkingen per seconde (IOPS) en de doorvoer van de on-premises schijven. Schijftypen zijn Azure Standard HDD, Azure Standard SSD en Azure Premium-schijven.
**As-is on-premises** | Beoordelingen die geen prestatiegegevens gebruiken om aanbevelingen te doen | De aanbeveling voor VM-grootte is gebaseerd op de on-premises VM-grootte.<br/><br> Het aanbevolen schijftype is gebaseerd op het geselecteerde opslagtype voor de beoordeling.

## <a name="how-do-i-run-an-assessment"></a>Hoe voer ik een assessment uit?

Er zijn een paar manieren om een beoordeling uit te voeren.

- Beoordeel machines met behulp van servermetadata die zijn verzameld door een lichtgewicht Azure Migrate-toestel. Het apparaat detecteert on-premises machines. Vervolgens worden machinemetagegevens en prestatiegegevens naar Azure Migrate verstuurt.
- Beoordeel machines met behulp van servermetagegevens die worden geïmporteerd in een CSV-indeling (comma-separated values).

## <a name="how-do-i-assess-with-the-appliance"></a>Hoe beoordeel ik met het apparaat?

Als u een Azure Migrate-toestel implementeert om on-premises servers te detecteren, gaat u de volgende stappen uitvoeren:

1. Stel Azure en uw on-premises omgeving in om te werken met serverbeoordeling.
1. Maak voor uw eerste beoordeling een Azure-project en voeg er het hulpprogramma voor serverbeoordeling aan toe.
1. Implementeer een lichtgewicht Azure Migrate-toestel. Het toestel detecteert continu on-premises machines en stuurt machinemetadata en prestatiegegevens naar Azure Migrate. Implementeer het apparaat als een VM of een fysieke machine. U hoeft niets te installeren op machines die u wilt beoordelen.

Nadat het apparaat met machinedetectie is begonnen, u machines verzamelen die u in een groep wilt beoordelen en een beoordeling voor de groep uitvoeren.

Volg onze tutorials voor [VMware,](tutorial-prepare-vmware.md) [Hyper-V](tutorial-prepare-hyper-v.md)of [fysieke servers](tutorial-prepare-physical.md) om deze stappen uit te proberen.

## <a name="how-do-i-assess-with-imported-data"></a>Hoe beoordeel ik met geïmporteerde gegevens?

Als u servers beoordeelt met behulp van een CSV-bestand, hebt u geen toestel nodig. Ga in plaats daarvan de volgende stappen uit:

1. Azure instellen voor gebruik met serverbeoordeling.
1. Maak voor uw eerste beoordeling een Azure-project en voeg er het hulpprogramma voor serverbeoordeling aan toe.
1. Download een CSV-sjabloon en voeg er servergegevens aan toe.
1. Importeer de sjabloon in Serverbeoordeling.
1. Ontdek servers die bij de import zijn toegevoegd, verzamel ze in een groep en voer een beoordeling uit voor de groep.

## <a name="what-data-does-the-appliance-collect"></a>Welke gegevens verzamelt het toestel?

Als u het Azure Migrate-toestel gebruikt voor beoordeling, leest u meer over de metagegevens en prestatiegegevens die zijn verzameld voor [VMware](migrate-appliance.md#collected-data---vmware) en [Hyper-V.](migrate-appliance.md#collected-data---hyper-v)

## <a name="how-does-the-appliance-calculate-performance-data"></a>Hoe berekent het toestel prestatiegegevens?

Als u het toestel voor detectie gebruikt, worden prestatiegegevens voor rekeninstellingen met de volgende stappen berekend:

1. Het apparaat verzamelt een real-time monsterpunt.

    - **VMware VM's**: Elke 20 seconden wordt een monsterpunt verzameld.
    - **Hyper-V VM's**: Elke 30 seconden wordt een monsterpunt verzameld.
    - **Fysieke servers**: Elke vijf minuten wordt een monsterpunt verzameld.

1. Het toestel combineert de monsterpunten om elke 10 minuten één gegevenspunt te maken. Als u het gegevenspunt wilt maken, selecteert het toestel de piekwaarden uit alle monsters. Vervolgens wordt het gegevenspunt naar Azure verstuurt.
1. Server assessment slaat alle gegevenspunten van 10 minuten van de afgelopen maand op.
1. Wanneer u een beoordeling maakt, identificeert Serverassessment het juiste gegevenspunt dat u moet gebruiken voor het claimen. De identificatie is gebaseerd op de percentielwaarden voor *prestatiegeschiedenis* en *percentielgebruik.*

    - Als de prestatiegeschiedenis bijvoorbeeld één week is en het percentielgebruik het 95e percentiel is, sorteert Serverassessment de voorbeeldpunten van 10 minuten voor de afgelopen week. Het sorteert ze in oplopende volgorde en kiest de 95e percentielwaarde voor het rightsizing.
    - De 95e percentielwaarde zorgt ervoor dat u uitschieters negeert, die kunnen worden opgenomen als u het 99e percentiel hebt gekozen.
    - Als u het piekgebruik voor de periode wilt kiezen en geen uitschieters wilt missen, selecteert u het 99e percentiel voor percentielgebruik.

1. Deze waarde wordt vermenigvuldigd met de comfortfactor om de effectieve prestatiegebruiksgegevens te krijgen voor deze statistieken die het toestel verzamelt:

    - CPU-gebruik
    - RAM-gebruik
    - Schijf IOPS (lezen en schrijven)
    - Schijfdoorvoer (lezen en schrijven)
    - Netwerkdoorvoer (in en uit)

## <a name="how-are-assessments-calculated"></a>Hoe worden beoordelingen berekend?

Server Assessment gebruikt de metadata- en prestatiegegevens van de on-premises machines om beoordelingen te berekenen. Als u het Azure Migrate-toestel implementeert, wordt in de beoordeling gebruik gemaakt van de gegevens die het toestel verzamelt. Maar als u een beoordeling uitvoert die is geïmporteerd met een CSV-bestand, geeft u de metagegevens voor de berekening.

Berekeningen vinden plaats in deze drie fasen:

1. **Azure-gereedheid berekenen:** beoordeel of machines geschikt zijn voor migratie naar Azure.
1. **Grootteaanbevelingen berekenen:** schat rekenkracht, opslag en netwerkgrootte.
1. **Maandelijkse kosten berekenen:** bereken de geschatte maandelijkse reken- en opslagkosten voor het uitvoeren van de machines in Azure na migratie.

Berekeningen zijn in de voorafgaande volgorde. Een machineserver wordt alleen naar een later stadium verplaatst als deze de vorige passeert. Als een server bijvoorbeeld de azure-gereedheidsfase uitvalt, is deze gemarkeerd als ongeschikt voor Azure. Grootte- en kostenberekeningen worden niet voor die server uitgevoerd.

## <a name="whats-in-an-assessment"></a>Waaruit bestaat een evaluatie?

Dit is wat er is opgenomen in een beoordeling in Serverassessment:

Eigenschap | Details
--- | ---
**Doellocatie** | De locatie waarnaar u wilt migreren. Serverassessment ondersteunt momenteel deze doelazure-regio's:<br/><br/> Australië Oost-Australië Zuidoost, Brazilië Zuid, Canada Centraal, Canada Oost, Centraal-India, Centraal-VS, China Oost-, China Noord-, Oost-Azië, Oost-VS, Oost-VS 2, Duitsland Centraal, Duitsland Noordoost, Japan Oost, Japan West, Korea Centraal, Korea Zuid, Noord-Centraal VS, Noord-Centraal VS, Zuidoost-Azië, Zuid-India, Verenigd Koninkrijk Zuid, Verenigd Koninkrijk West, VS Gov Arizona, US Gov Texas, US Gov Virginia , West Central US, West-Europa, West-India, West-VS en West US 2.
**Doelopslagschijf (as-is-sizing)** | Het type schijf dat moet worden gebruikt voor opslag in Azure. <br/><br/> Geef de doelopslagschijf op als door Premium beheerd, standaard ssd-beheerd of standaard hdd-beheerd.
**Doelopslagschijf (prestatiegebaseerde grootte)** | Hiermee geeft u het type doelopslagschijf op als automatisch, door Premium beheerd, standaard hdd-beheerd of standaard ssd-beheerd.<br/><br/> **Automatisch**: De schijfaanbeveling is gebaseerd op de prestatiegegevens van de schijven, wat de IOPS en doorvoer betekent.<br/><br/>**Premium of Standaard:** De beoordeling beveelt een schijf SKU binnen het geselecteerde opslagtype aan.<br/><br/> Als u een SLA (SLA) met één instantie (SLA) van 99,9% wilt, u overwegen schijven met een malige koppeling sait(SLA) van 99,9% te gebruiken. Dit gebruik zorgt ervoor dat alle schijven in de beoordeling worden aanbevolen als schijven die door Premium worden beheerd.<br/><br/> Azure Migrate ondersteunt alleen beheerde schijven voor migratiebeoordeling.
**Azure Gereserveerde virtuele machine-exemplaren** | Hiermee geeft u [gereserveerde exemplaren](https://azure.microsoft.com/pricing/reserved-vm-instances/) op, zodat bij kostenschattingen in de beoordeling rekening wordt gehouden.<br/><br/> Azure Migrate ondersteunt momenteel Azure Reserved VM Instances alleen voor betalen per gebruik-aanbiedingen.
**Groottecriteria** | Wordt gebruikt om de Azure VM te rightsizen.<br/><br/> Gebruik as-is-maatvoering of prestatiegebaseerde grootte.
**Prestatiegeschiedenis** | Gebruikt met prestatiegebaseerde maatvoering. Prestatiegeschiedenis geeft de duur op die wordt gebruikt wanneer prestatiegegevens worden geëvalueerd.
**Percentiel gebruik** | Gebruikt met prestatiegebaseerde maatvoering. Percentielgebruik geeft de percentielwaarde op van het prestatievoorbeeld dat wordt gebruikt voor het claimen.
**VM-reeks** | De Azure VM-serie die u wilt overwegen voor het rechten geven. Als u bijvoorbeeld geen productieomgeving hebt waarvoor VM's uit de A-serie in Azure nodig zijn, u A-reeksen uitsluiten van de lijst met reeksen.
**Comfortfactor** | De buffer die wordt gebruikt tijdens de beoordeling. Het wordt toegepast op de CPU-, RAM-, schijf- en netwerkgebruiksgegevens voor VM's. Het is goed voor problemen zoals seizoensgebonden gebruik, korte prestatiegeschiedenis en waarschijnlijke toename van toekomstig gebruik.<br/><br/> Een 10-core VM met 20% gebruik resulteert bijvoorbeeld normaal gesproken in een tweecore VM. Met een comfortfactor van 2.0 is het resultaat een vier-core VM in plaats daarvan.
**Aanbieding** | De [Azure-aanbieding](https://azure.microsoft.com/support/legal/offer-details/) waarin u bent ingeschreven. Serverassessment schat de kosten voor die aanbieding.
**Valuta** | De factureringsvaluta voor uw account.
**Korting (%)** | Alle abonnementsspecifieke kortingen die u bovenop de Azure-aanbieding ontvangt. De standaardinstelling is 0%.
**VM tijd actief** | De duur in dagen per maand en uren per dag voor Azure VM's die niet continu worden uitgevoerd. Kostenramingen zijn gebaseerd op die duur.<br/><br/> De standaardwaarden zijn 31 dagen per maand en 24 uur per dag.
**Azure Hybrid Benefit** | Hiermee geeft u op of u softwarezekerheid hebt en in aanmerking komt voor [Azure Hybrid Benefit.](https://azure.microsoft.com/pricing/hybrid-use-benefit/) Als de instelling de standaardwaarde 'Ja' heeft, worden Azure-prijzen voor andere besturingssystemen dan Windows in aanmerking genomen voor Windows VM's.

[Bekijk de aanbevolen procedures](best-practices-assessment.md) voor het maken van een beoordeling met Serverassessment.

## <a name="calculate-readiness"></a>Gereedheid berekenen

Niet alle machines zijn geschikt om in Azure te draaien. Server Assessment beoordeelt alle on-premises machines en wijst ze een gereedheidscategorie toe.

- **Klaar voor Azure**: de machine kan worden gemigreerd naar Azure zonder wijzigingen. Het begint in Azure met volledige Azure-ondersteuning.
- **Voorwaardelijk klaar voor Azure:** de machine start mogelijk in Azure, maar heeft mogelijk geen volledige Azure-ondersteuning. Azure biedt bijvoorbeeld geen ondersteuning voor een machine waarop een oude versie van Windows Server wordt uitgevoerd. U moet voorzichtig zijn voordat u deze machines migreert naar Azure. Om eventuele gereedheidsproblemen op te lossen, volgt u de herstelrichtlijnen die de beoordeling suggereert.
- **Niet klaar voor Azure**: De machine start niet in Azure. Als de schijf van een on-premises machine bijvoorbeeld meer dan 64 TB opslaat, kan Azure de machine niet hosten. Volg de herstelrichtlijnen om het probleem op te lossen voordat u de migratie aangaat.
- **Gereedheid onbekend**: Azure Migrate kan de gereedheid van de machine niet bepalen vanwege onvoldoende metagegevens.

Om de gereedheid te berekenen, controleert Server Assessment de machine-eigenschappen en de instellingen van het besturingssysteem die zijn samengevat in de volgende tabellen.

### <a name="machine-properties"></a>Machine-eigenschappen

Serverassessment controleert de volgende eigenschappen van een on-premises VM om te bepalen of deze op Azure kan worden uitgevoerd.

Eigenschap | Details | Azure-gereedheidsstatus
--- | --- | ---
**Opstarttype** | Azure ondersteunt VM's met een opstarttype BIOS, niet Met UEFI. | Voorwaardelijk klaar als het opstarttype UEFI is
**Kernen** | Elke machine mag niet meer dan 128 cores hebben, wat het maximale aantal is dat een Azure VM ondersteunt.<br/><br/> Als de prestatiegeschiedenis beschikbaar is, houdt Azure Migrate rekening met de gebruikte kernen ter vergelijking. Als de beoordelingsinstellingen een comfortfactor opgeven, wordt het aantal gebruikte kernen vermenigvuldigd met de comfortfactor.<br/><br/> Als er geen prestatiegeschiedenis is, gebruikt Azure Migrate de toegewezen kernen zonder de comfortfactor toe te passen. | Klaar als het aantal kernen binnen de limiet is
**Ram** | Elke machine mag niet meer dan 3.892 GB RAM hebben, wat&nbsp;de maximale grootte is die een Azure M-serie Standard_M128m<sup>2</sup> VM ondersteunt. [Meer informatie](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Als prestatiegeschiedenis beschikbaar is, houdt Azure Migrate rekening met het gebruikte RAM-geheugen ter vergelijking. Als een comfortfactor wordt opgegeven, wordt het gebruikte RAM-geheugen vermenigvuldigd met de comfortfactor.<br/><br/> Als er geen geschiedenis is, wordt het toegewezen RAM-geheugen gebruikt zonder toepassing van een comfortfactor.<br/><br/> | Klaar als de hoeveelheid RAM binnen de limiet ligt
**Opslagschijf** | De toegewezen grootte van een schijf mag niet meer dan 32 TB bedragen. Hoewel Azure 64 TB-schijven ondersteunt met Azure Ultra SSD-schijven, controleert Azure Migrate: Server Assessment momenteel op 32 TB als schijfgroottelimiet omdat het Ultra SSD nog niet ondersteunt. <br/><br/> Het aantal schijven dat aan de machine is gekoppeld, inclusief de osschijf, moet 65 of minder zijn. | Klaar als de schijfgrootte en het nummer binnen de limieten liggen
**Netwerken** | Een machine mag niet meer dan 32 netwerkinterfaces (NIC's) aan de machine hebben. | Klaar als het aantal NIC's binnen de limiet ligt

### <a name="guest-operating-system"></a>Gastbesturingssysteem

Samen met het controleren van VM-eigenschappen kijkt Server Assessment naar het gastbesturingssysteem van een machine om te bepalen of deze op Azure kan worden uitgevoerd.

> [!NOTE]
> Voor het afhandelen van gastanalyse voor Vm's van VMware gebruikt Server Assessment het besturingssysteem dat is opgegeven voor de VM in vCenter Server. Voor Linux VM's die op VMware worden uitgevoerd, identificeert Server Assessment momenteel niet de kernelversie van het gastbesturingssysteem.

Serverbeoordeling gebruikt de volgende logica om azure-gereedheid te identificeren op basis van het besturingssysteem:

**Besturingssysteem** | **Details** | **Azure-gereedheidsstatus**
--- | --- | ---
Windows Server 2016 en alle SP's | Azure biedt volledige ondersteuning. | Klaar voor Azure.
Windows Server 2012 R2 en alle SP's | Azure biedt volledige ondersteuning. | Klaar voor Azure.
Windows Server 2012 en alle SP's | Azure biedt volledige ondersteuning. | Klaar voor Azure.
Windows Server 2008 R2 met alle SP's | Azure biedt volledige ondersteuning.| Klaar voor Azure.
Windows Server 2008 (32-bits en 64-bits) | Azure biedt volledige ondersteuning. | Klaar voor Azure.
Windows Server 2003 en Windows Server 2003 R2 | Deze besturingssystemen zijn geslaagd voor hun einddatum voor ondersteuning en hebben een [Aangepaste Ondersteuningsovereenkomst (CSA)](https://aka.ms/WSosstatement) nodig voor ondersteuning in Azure. | Voorwaardelijk klaar voor Azure. Overweeg het besturingssysteem te upgraden voordat u migreert naar Azure.
Windows 2000, Windows 98, Windows 95, Windows NT, Windows 3.1 en MS-DOS | Deze besturingssystemen zijn geslaagd voor hun einddatum. De machine kan starten in Azure, maar Azure biedt geen OS-ondersteuning. | Voorwaardelijk klaar voor Azure. We raden u aan het besturingssysteem te upgraden voordat u migreert naar Azure.
Windows 7, Windows 8 en Windows 10 | Azure biedt alleen ondersteuning met een [Visual Studio-abonnement.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Voorwaardelijk klaar voor Azure.
Windows 10 Pro | Azure biedt ondersteuning met [Multitenant Hosting Rights.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Voorwaardelijk klaar voor Azure.
Windows Vista en Windows XP Professional | Deze besturingssystemen zijn geslaagd voor hun einddatum. De machine kan starten in Azure, maar Azure biedt geen OS-ondersteuning. | Voorwaardelijk klaar voor Azure. We raden u aan het besturingssysteem te upgraden voordat u migreert naar Azure.
Linux | Bekijk de [Linux-besturingssystemen](../virtual-machines/linux/endorsed-distros.md) die Azure onderschrijft. Andere Linux-besturingssystemen kunnen starten in Azure. We raden u echter aan het besturingssysteem te upgraden naar een goedgekeurde versie voordat u migreert naar Azure. | Klaar voor Azure als de versie wordt goedgekeurd.<br/><br/>Voorwaardelijk klaar als de versie niet wordt goedgekeurd.
Andere besturingssystemen zoals Oracle Solaris, Apple macOS en FreeBSD | Azure onderschrijft deze besturingssystemen niet. De machine kan starten in Azure, maar Azure biedt geen OS-ondersteuning. | Voorwaardelijk klaar voor Azure. We raden u aan een ondersteund besturingssysteem te installeren voordat u naar Azure migreert.  
BE opgegeven als **Ander** in vCenter Server | Azure Migrate kan het besturingssysteem in dit geval niet identificeren. | Onbekende bereidheid. Controleer of Azure het besturingssysteem ondersteunt dat in de VM wordt uitgevoerd.
32-bits besturingssystemen | De machine start mogelijk in Azure, maar Azure biedt mogelijk geen volledige ondersteuning. | Voorwaardelijk klaar voor Azure. Overweeg een upgrade naar een 64-bits besturingssysteem voordat u migreert naar Azure.

## <a name="calculating-sizing"></a>Het berekenen van de grootte

Nadat de machine is gemarkeerd als klaar voor Azure, doet Server Assessment maataanbevelingen. Deze aanbevelingen identificeren de Azure VM en disk SKU. Grootteberekeningen zijn afhankelijk van de vraag of u as-is on-premises grootte of prestatiegebaseerde grootte gebruikt.

### <a name="calculate-sizing-as-is-on-premises"></a>Grootte berekenen (on-premises)

 Als u on-premises grootte sizing gebruikt, houdt Server Assessment geen rekening met de prestatiegeschiedenis van de VM's en schijven.

- **Compute-grootte:** Serverassessment wijst een Azure VM SKU toe op basis van de toegewezen grootte on-premises.
- **Opslag- en schijfgrootte :** Serverbeoordeling kijkt naar het opslagtype dat is opgegeven in beoordelingseigenschappen en beveelt het juiste schijftype aan. Mogelijke opslagtypen zijn Standard HDD, Standard SSD en Premium. Het standaardopslagtype is Premium.
- **Netwerkgrootte**: Serverassessment houdt rekening met de netwerkadapter op de on-premises machine.

### <a name="calculate-sizing-performance-based"></a>Grootte berekenen (op basis van prestaties)

Als u prestatiegebaseerde grootte gebruikt, doet Server Assessment als volgt maataanbevelingen:

- Serverbeoordeling houdt rekening met de prestatiegeschiedenis van de machine om de VM-grootte en het schijftype in Azure te identificeren.
- Als u servers importeert met een CSV-bestand, worden de opgegeven waarden gebruikt. Deze methode is vooral handig als u de on-premises machine overbezet hebt, het gebruik laag is en u de Azure VM wilt rechtzetten om kosten te besparen.
- Als u de prestatiegegevens niet wilt gebruiken, stelt u de groottecriteria opnieuw in op de on-premises as-is, zoals beschreven in de vorige sectie.

#### <a name="calculate-storage-sizing"></a>Opslaggrootte berekenen

Voor opslaggrootte probeert Azure Migrate elke schijf die aan de machine is gekoppeld, aan een Azure-schijf te toewijzen. Het dimensioneren werkt als volgt:

1. Server Assessment voegt de lees- en schrijf-IOPS van een schijf toe om de totale Vereiste IOPS te krijgen. Op dezelfde manier worden de lees- en schrijfdoorvoerwaarden toegevoegd om de totale doorvoer van elke schijf te krijgen.
1. Als u het opslagtype als automatisch hebt opgegeven, is het geselecteerde type gebaseerd op de effectieve IOPS- en doorvoerwaarden. Serverbeoordeling bepaalt of de schijf moet worden toegewezen aan een standaardhdd-, standaardSSD- of Premium-schijf in Azure. Als het opslagtype is ingesteld op een van deze schijftypen, probeert Serverassessment een schijf-SKU te vinden binnen het geselecteerde opslagtype.
1. Schijven worden als volgt geselecteerd:
    - Als Server Assessment geen schijf met de vereiste IOPS en doorvoer kan vinden, wordt de machine als ongeschikt voor Azure beschouwd.
    - Als serverbeoordeling een set geschikte schijven vindt, selecteert u de schijven die de locatie ondersteunen die is opgegeven in de beoordelingsinstellingen.
    - Als er meerdere in aanmerking komende schijven zijn, selecteert Serverassessment de schijf met de laagste kosten.
    - Als prestatiegegevens voor een schijf niet beschikbaar zijn, wordt de configuratieschijfgrootte gebruikt om een standaard SSD-schijf in Azure te vinden.

#### <a name="calculate-network-sizing"></a>Netwerkgrootte berekenen

Serverassessment probeert een Azure VM te vinden die het aantal en de vereiste prestaties van netwerkadapters ondersteunt die zijn gekoppeld aan de on-premises machine.

- Om de effectieve netwerkprestaties van de on-premises VM te krijgen, verzamelt Server Assessment de gegevenstransmissiesnelheid uit de machine (netwerk uit) over alle netwerkadapters. Het past dan de comfortfactor toe. Het gebruikt de resulterende waarde om een Azure VM te vinden die de vereiste netwerkprestaties kan ondersteunen.
- Naast de netwerkprestaties wordt bij Server Assessment ook gekeken of de Azure VM het vereiste aantal netwerkadapters kan ondersteunen.
- Als netwerkprestatiegegevens niet beschikbaar zijn, houdt Serverassessment rekening met alleen het aantal netwerkadapters voor vm-grootte.

#### <a name="calculate-compute-sizing"></a>Compute-grootte berekenen

Nadat de opslag- en netwerkvereisten zijn berekend, houdt Server Assessment rekening met CPU- en RAM-vereisten om een geschikte VM-grootte in Azure te vinden.

- Azure Migrate kijkt naar de effectieve gebruikte kernen en RAM om een geschikte Azure VM-grootte te vinden.
- Als er geen geschikte grootte wordt gevonden, wordt de machine gemarkeerd als ongeschikt voor Azure.
- Als er een geschikte grootte wordt gevonden, past Azure Migrate de opslag- en netwerkberekeningen toe. Vervolgens worden locatie- en prijsrijinstellingen voor de uiteindelijke aanbeveling voor vm-grootte van toepassing.
- Als er meer Azure VM-grootten in aanmerking komen, wordt de grootte met de laagste kosten aanbevolen.

## <a name="confidence-ratings-performance-based"></a>Vertrouwensbeoordelingen (op basis van prestaties)

Elke prestatiegebaseerde beoordeling in Azure Migrate is gekoppeld aan een betrouwbaarheidsclassificatie. De beoordeling varieert van één (laagste) tot vijf (hoogste) sterren. Met de betrouwbaarheidsclassificatie u de betrouwbaarheid schatten van de grootteaanbevelingen die Azure Migrate biedt.

- De betrouwbaarheidsbeoordeling wordt toegewezen aan een beoordeling. De beoordeling is gebaseerd op de beschikbaarheid van gegevenspunten die nodig zijn om de beoordeling te berekenen.
- Voor prestatiegebaseerde grootte, serverbeoordeling behoeften:
    - De gebruiksgegevens voor CPU en VM RAM.
    - De schijf-IOPS- en doorvoergegevens voor elke schijf die aan de VM is gekoppeld.
    - De netwerk-I/O voor het verwerken van prestatiegebaseerde grootte voor elke netwerkadapter die is gekoppeld aan een vm.

Als een van deze gebruiksnummers niet beschikbaar is, zijn de aanbevelingen voor grootte mogelijk onbetrouwbaar.

> [!NOTE]
> Vertrouwensclassificaties worden niet toegewezen voor servers die worden beoordeeld met een geïmporteerd CSV-bestand. Beoordelingen zijn ook niet van toepassing op on-premises beoordeling.

### <a name="ratings"></a>Waarderingen

In deze tabel worden de beoordelingen van het beoordelingsvertrouwen weergegeven, die afhankelijk zijn van het percentage beschikbare gegevenspunten:

   **Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
   --- | ---
   0-20% | 1 ster
   21-40% | 2 sterren
   41-60% | 3 sterren
   61-80% | 4 sterren
   81-100% | 5 sterren

### <a name="low-confidence-ratings"></a>Lage vertrouwensbeoordelingen

Hier zijn een paar redenen waarom een beoordeling een lage betrouwbaarheidsscore zou kunnen krijgen:

- Je hebt je omgeving niet geprofileerd voor de duur waarvoor je de beoordeling maakt. Als u bijvoorbeeld de beoordeling maakt waarbij de prestatieduur is ingesteld op één dag, moet u ten minste een dag wachten nadat u begint met de detectie voordat alle gegevenspunten worden verzameld.
- Sommige VM's werden stilgelegd gedurende de tijd waarvoor de beoordeling werd berekend. Als vm's voor enige tijd zijn uitgeschakeld, kan Server assessment de prestatiegegevens voor die periode niet verzamelen.
- Sommige VM's zijn gemaakt gedurende de tijd waarvoor de beoordeling werd berekend. Stel dat u een beoordeling hebt gemaakt voor de prestatiegeschiedenis van de afgelopen maand, maar dat sommige VM's pas een week geleden zijn gemaakt. De prestatiegeschiedenis van de nieuwe VM's bestaat niet voor de volledige duur.

> [!NOTE]
> Als de betrouwbaarheidsbeoordeling van een beoordeling minder dan vijf sterren bedraagt, raden we u aan ten minste een dag te wachten voordat het apparaat de omgeving heeft geprofileerd en vervolgens de beoordeling opnieuw te berekenen. Anders kan prestatiegebaseerde grootte onbetrouwbaar zijn. In dat geval raden we u aan de beoordeling over te schakelen op on-premises dimensionering.

## <a name="calculate-monthly-costs"></a>Maandelijkse kosten berekenen

Nadat de aanbevelingen voor het dimensioneren zijn voltooid, berekent Azure Migrate reken- en opslagkosten voor na migratie.

- **Rekenkosten:** Azure Migrate gebruikt de aanbevolen Azure VM-grootte en de Azure Billing API om de maandelijkse kosten voor de VM te berekenen.

    Bij de berekening wordt rekening gehouden met:
    - Besturingssysteem
    - Softwarezekerheid
    - Gereserveerde instanties
    - VM tijd actief
    - Locatie
    - Valuta-instellingen

    Serverbeoordeling verzamelt de kosten voor alle machines om de totale maandelijkse rekenkosten te berekenen.

- **Opslagkosten:** De maandelijkse opslagkosten voor een machine worden berekend door de maandelijkse kosten van alle schijven die aan de machine zijn gekoppeld, te aggregeren.

    Server assessment berekent de totale maandelijkse opslagkosten door de opslagkosten van alle machines te aggregeren. Momenteel houdt de berekening geen rekening met aanbiedingen die zijn opgegeven in de beoordelingsinstellingen.

De kosten worden weergegeven in de valuta die is opgegeven in de beoordelingsinstellingen.

## <a name="next-steps"></a>Volgende stappen

[Bekijk](best-practices-assessment.md) de aanbevolen procedures voor het maken van beoordelingen. 

- Meer informatie over het uitvoeren van beoordelingen voor [Vm's van VMware,](tutorial-prepare-vmware.md) [Hyper-V VM's](tutorial-prepare-hyper-v.md)en [fysieke servers](tutorial-prepare-physical.md).
- Meer informatie over het beoordelen van servers [die zijn geïmporteerd met een CSV-bestand](tutorial-assess-import.md).
- Meer informatie over het instellen van [afhankelijkheidsvisualisatie](concepts-dependency-visualization.md).
