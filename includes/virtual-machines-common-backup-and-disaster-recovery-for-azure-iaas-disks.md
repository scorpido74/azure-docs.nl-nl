---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: aa7ddb75017a532b436b9a5cfc71d1a7c2832cb6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77179014"
---
In dit artikel wordt uitgelegd hoe u back-up- en disaster recovery (DR) van IaaS-virtuele machines (VM's) en schijven in Azure plannen. Dit document heeft betrekking op zowel beheerde als onbeheerde schijven.

Ten eerste behandelen we de ingebouwde fouttolerantiemogelijkheden in het Azure-platform die helpen bij het beschermen tegen lokale storingen. Vervolgens bespreken we de rampscenario's die niet volledig onder de ingebouwde mogelijkheden vallen. We tonen ook verschillende voorbeelden van workloadscenario's waar verschillende back-up- en DR-overwegingen van toepassing kunnen zijn. Vervolgens bekijken we mogelijke oplossingen voor de DR van IaaS-schijven.

## <a name="introduction"></a>Inleiding

Het Azure-platform gebruikt verschillende methoden voor redundantie en fouttolerantie om klanten te beschermen tegen gelokaliseerde hardwarefouten. Lokale fouten kunnen problemen met een Azure Storage-serverbevattende bevatten die een deel van de gegevens opslaat voor een virtuele schijf of storingen van een SSD of HDD op die server. Dergelijke geïsoleerde hardware component storingen kunnen optreden tijdens normale bewerkingen.

Het Azure-platform is ontworpen om bestand te zijn tegen deze fouten. Grote rampen kunnen leiden tot storingen of de ontoegankelijkheid van veel opslagservers of zelfs een heel datacenter. Hoewel uw VM's en schijven normaal gesproken worden beschermd tegen gelokaliseerde fouten, zijn extra stappen nodig om uw werkbelasting te beschermen tegen catastrofale fouten in de hele regio, zoals een grote ramp, die van invloed kunnen zijn op uw VM en schijven.

Naast de mogelijkheid van platformfouten kunnen er problemen optreden met een klanttoepassing of gegevens. Een nieuwe versie van uw toepassing kan bijvoorbeeld per ongeluk een wijziging aanbrengen in de gegevens waardoor deze wordt afgebroken. In dat geval u de toepassing en de gegevens terugzetten naar een eerdere versie die de laatst bekende goede status bevat. Dit vereist het onderhouden van regelmatige back-ups.

Voor regionaal herstel na noodgevallen moet u een back-up maken van uw IaaS VM-schijven naar een andere regio.

Voordat we kijken naar back-up- en DR-opties, moeten we een aantal methoden samenvatten die beschikbaar zijn voor het afhandelen van gelokaliseerde fouten.

### <a name="azure-iaas-resiliency"></a>Azure IaaS-tolerantie

*Tolerantie* verwijst naar de tolerantie voor normale fouten die optreden in hardwarecomponenten. Tolerantie is de mogelijkheid om te herstellen van storingen en blijven functioneren. Het gaat niet om het vermijden van storingen, maar om het reageren op storingen op een manier waarmee downtime of gegevensverlies wordt voorkomen. Het doel van flexibiliteit is om de toepassing na een storing weer volledig te laten functioneren. Azure virtuele machines en schijven zijn ontworpen om bestand te zijn tegen veelvoorkomende hardwarefouten. Laten we eens kijken hoe het Azure IaaS-platform deze veerkracht biedt.

Een virtuele machine bestaat voornamelijk uit twee delen: een compute server en de persistente schijven. Beide beïnvloeden de fouttolerantie van een virtuele machine.

Als de Azure compute host-server waarin uw VM wordt geplaatst, een hardwarefout ondervindt, wat zeldzaam is, is Azure ontworpen om de VM automatisch op een andere server te herstellen. Als dit scenario wordt opnieuw opgestart en wordt de VM na enige tijd weer opgestart. Azure detecteert automatisch dergelijke hardwarefouten en voert herstelacties uit om ervoor te zorgen dat de VM van de klant zo snel mogelijk beschikbaar is.

Wat IaaS-schijven betreft, is de duurzaamheid van gegevens van cruciaal belang voor een persistent opslagplatform. Azure-klanten hebben belangrijke bedrijfstoepassingen die op IaaS worden uitgevoerd en zijn afhankelijk van de persistentie van de gegevens. Azure ontwerpt beveiliging voor deze IaaS-schijven, met drie redundante kopieën van de gegevens die lokaal worden opgeslagen. Deze kopieën zorgen voor een hoge duurzaamheid tegen lokale storingen. Als een van de hardwareonderdelen die uw schijf bevat, mislukt, wordt uw vm niet beïnvloed, omdat er twee extra exemplaren zijn om schijfaanvragen te ondersteunen. Het werkt prima, zelfs als twee verschillende hardwarecomponenten die een schijf ondersteunen tegelijkertijd mislukken (wat zeldzaam is). 

Om ervoor te zorgen dat u altijd drie replica's onderhoudt, geeft Azure Storage automatisch een nieuwe kopie van de gegevens op de achtergrond als een van de drie kopieën niet meer beschikbaar is. Daarom is het niet nodig om RAID met Azure-schijven te gebruiken voor fouttolerantie. Een eenvoudige RAID 0-configuratie moet voldoende zijn voor het strippen van de schijven, indien nodig, om grotere volumes te creëren.

Vanwege deze architectuur heeft Azure consistent enterprise-grade duurzaamheid geleverd voor IaaS-schijven, met een toonaangevend [e-annualized failure rate.](https://en.wikipedia.org/wiki/Annualized_failure_rate)

Gelokaliseerde hardwarefouten op de compute host of in het opslagplatform kunnen soms resulteren in de tijdelijke onbeschikbaarheid van de VM die wordt gedekt door de [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) voor VM-beschikbaarheid. Azure biedt ook een toonaangevende SLA voor afzonderlijke VM-exemplaren die gebruikmaken van Azure premium SSD's.

Om de werkbelasting van toepassingen te beschermen tegen downtime vanwege de tijdelijke onbeschikbaarheid van een schijf of vm, kunnen klanten [beschikbaarheidssets](../articles/virtual-machines/windows/manage-availability.md)gebruiken. Twee of meer virtuele machines in een beschikbaarheidsset bieden redundantie voor de toepassing. Azure maakt vervolgens deze VM's en schijven in afzonderlijke foutdomeinen met verschillende energie-, netwerk- en servercomponenten.

Vanwege deze afzonderlijke foutdomeinen hebben gelokaliseerde hardwarefouten doorgaans geen invloed op meerdere VM's in de set tegelijkertijd. Het hebben van afzonderlijke foutdomeinen biedt hoge beschikbaarheid voor uw toepassing. Het wordt beschouwd als een goede gewoonte om beschikbaarheidssets te gebruiken wanneer hoge beschikbaarheid vereist is. Het volgende gedeelte behandelt het disaster recovery aspect.

### <a name="backup-and-disaster-recovery"></a>Back-up en herstel na noodgevallen

Disaster recovery is het vermogen om te herstellen van zeldzame, maar grote, incidenten. Deze incidenten omvatten niet-tijdelijke, grootschalige storingen, zoals serviceonderbrekingen die een hele regio beïnvloeden. Disaster recovery omvat back-up en archivering van gegevens, en kan handmatige interventie omvatten, zoals het herstellen van een database vanuit een back-up.

De ingebouwde beveiliging van het Azure-platform tegen gelokaliseerde storingen beschermt de VM's/schijven mogelijk niet volledig als een grote ramp grootschalige uitval veroorzaakt. Deze grootschalige uitval omvat catastrofale gebeurtenissen, zoals als een datacenter wordt getroffen door een orkaan, aardbeving, brand, of als er een grootschalige hardware-eenheid storing. Bovendien u fouten tegenkomen als gevolg van problemen met de toepassing of gegevens.

Om uw IaaS-workloads te beschermen tegen uitval, moet u redundantie plannen en back-ups hebben om herstel mogelijk te maken. Voor herstel na noodgevallen moet u een back-up maken op een andere geografische locatie, weg van de primaire site. Deze aanpak zorgt ervoor dat uw back-up niet wordt beïnvloed door dezelfde gebeurtenis die oorspronkelijk van invloed was op de VM of schijven. Zie [Disaster recovery voor Azure-toepassingen voor](/azure/architecture/resiliency/disaster-recovery-azure-applications)meer informatie.

Uw DR-overwegingen kunnen de volgende aspecten omvatten:

- Hoge beschikbaarheid: het vermogen van de toepassing om in een gezonde staat te blijven werken, zonder aanzienlijke downtime. Door *een gezonde status*betekent deze status dat de toepassing responsief is en dat gebruikers verbinding kunnen maken met de toepassing en ermee kunnen communiceren. Bepaalde bedrijfskritieke toepassingen en databases moeten mogelijk altijd beschikbaar zijn, zelfs als er fouten zijn in het platform. Voor deze workloads moet u mogelijk redundantie plannen voor de toepassing en de gegevens.

- Duurzaamheid van gegevens: In sommige gevallen is de belangrijkste overweging ervoor te zorgen dat de gegevens worden bewaard als er een ramp plaatsvindt. Daarom hebt u mogelijk een back-up van uw gegevens op een andere site nodig. Voor dergelijke workloads hebt u mogelijk geen volledige redundantie nodig voor de toepassing, maar alleen een regelmatige back-up van de schijven.

## <a name="backup-and-dr-scenarios"></a>Back-up- en DR-scenario's

Laten we eens kijken naar een paar typische voorbeelden van scenario's voor toepassingswerkbelasting en de overwegingen voor het plannen voor herstel na noodgevallen.

### <a name="scenario-1-major-database-solutions"></a>Scenario 1: Belangrijke databaseoplossingen

Overweeg een productiedatabaseserver, zoals SQL Server of Oracle, die hoge beschikbaarheid kan ondersteunen. Kritieke productietoepassingen en gebruikers zijn afhankelijk van deze database. Het noodherstelplan voor dit systeem moet mogelijk de volgende vereisten ondersteunen:

- De gegevens moeten worden beschermd en hersteld.
- De server moet beschikbaar zijn voor gebruik.

Het noodherstelplan vereist mogelijk het onderhouden van een replica van de database in een andere regio als back-up. Afhankelijk van de vereisten voor de beschikbaarheid van de server en het herstel van gegevens, kan de oplossing variëren van een actief actieve of actief-passieve replicasite tot periodieke offline back-ups van de gegevens. Relationele databases, zoals SQL Server en Oracle, bieden verschillende opties voor replicatie. Gebruik [SQL Server AlwaysOn Availability Groups](https://msdn.microsoft.com/library/hh510230.aspx) voor hoge beschikbaarheid voor SQL Server.

NoSQL-databases ondersteunen, zoals MongoDB, ook [replica's](https://docs.mongodb.com/manual/replication/) voor redundantie. De replica's voor hoge beschikbaarheid worden gebruikt.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Scenario 2: Een cluster van redundante VM's

Overweeg een werkbelasting die wordt beheerd door een cluster van VM's die redundantie en taakverdeling bieden. Een voorbeeld is een Cassandra-cluster dat in een regio wordt geïmplementeerd. Dit type architectuur zorgt al voor een hoge redundantie binnen die regio. Om de werkbelasting echter te beschermen tegen een fout op regionaal niveau, moet u overwegen het cluster over twee regio's te verspreiden of periodieke back-ups naar een andere regio te maken.

### <a name="scenario-3-iaas-application-workload"></a>Scenario 3: Werkbelasting van IaaS-toepassingen

Laten we eens kijken naar de IaaS applicatie workload. Deze toepassing kan bijvoorbeeld een typische productiewerkbelasting zijn die wordt uitgevoerd op een Azure-vm. Het kan een webserver of bestandsserver zijn die de inhoud en andere bronnen van een site vasthoudt. Het kan ook een op maat gemaakte bedrijfstoepassing zijn die wordt uitgevoerd op een VM die de gegevens, resources en toepassingsstatus op de VM-schijven heeft opgeslagen. In dit geval is het belangrijk om ervoor te zorgen dat u regelmatig back-ups maakt. De back-upfrequentie moet gebaseerd zijn op de aard van de VM-werkbelasting. Als de toepassing bijvoorbeeld elke dag wordt uitgevoerd en gegevens wijzigt, moet de back-up elk uur worden genomen.

Een ander voorbeeld is een rapportageserver die gegevens uit andere bronnen haalt en geaggregeerde rapporten genereert. Het verlies van deze VM of schijven kan leiden tot het verlies van de rapporten. Het is echter mogelijk om het rapportageproces opnieuw uit te voeren en de uitvoer te regenereren. In dat geval hebt u niet echt een verlies van gegevens, zelfs als de rapportageserver wordt getroffen door een ramp. Als gevolg hiervan u een hoger tolerantieniveau hebben voor het verliezen van een deel van de gegevens op de rapportageserver. In dat geval zijn minder frequente back-ups een optie om de kosten te verlagen.

### <a name="scenario-4-iaas-application-data-issues"></a>Scenario 4: Problemen met de gegevens van iaaS-toepassingen

IaaS applicatie gegevens problemen zijn een andere mogelijkheid. Overweeg een toepassing die kritieke commerciële gegevens berekent, onderhoudt en bedient, zoals prijsinformatie. Een nieuwe versie van uw toepassing had een softwarebug die de prijzen onjuist berekende en de bestaande handelsgegevens van het platform corrumpeerde. Hier, de beste manier van handelen is om terug te keren naar de eerdere versie van de toepassing en de gegevens. Neem hiervoor periodieke back-ups van uw systeem.

## <a name="disaster-recovery-solution-azure-backup"></a>Oplossing voor noodherstel: Azure Backup 

[Azure Backup](https://azure.microsoft.com/services/backup/) wordt gebruikt voor back-ups en DR, en het werkt met [beheerde schijven](../articles/virtual-machines/windows/managed-disks-overview.md) en onbeheerde schijven. U een back-uptaak maken met op tijd gebaseerde back-ups, eenvoudig vm-herstel- en back-upbeleid.

Als u [premium SSD's,](../articles/virtual-machines/windows/disks-types.md) [beheerde schijven](../articles/virtual-machines/windows/managed-disks-overview.md)of andere schijftypen gebruikt met de lokaal [redundante opslagoptie,](../articles/storage/common/storage-redundancy-lrs.md) is het vooral belangrijk om periodieke DR-back-ups te maken. Azure Backup slaat de gegevens op in de kluis van uw herstelservices voor langdurige bewaring. Kies de [optie voor georedundante opslag](../articles/storage/common/storage-redundancy-grs.md) voor de kluis van back-upherstelservices. Deze optie zorgt ervoor dat back-ups worden gerepliceerd naar een andere Azure-regio om te beschermen tegen regionale rampen.

Voor niet-beheerde schijven u het lokaal redundante opslagtype voor IaaS-schijven gebruiken, maar ervoor zorgen dat Azure Backup is ingeschakeld met de georedundante opslagoptie voor de kluis van herstelservices.

> [!NOTE]
> Als u de [georedundante opslag-](../articles/storage/common/storage-redundancy-grs.md) of [georedundante opslagoptie](../articles/storage/common/storage-redundancy.md) voor lezen voor uw niet-beheerde schijven gebruikt, hebt u nog steeds consistente momentopnamen nodig voor back-ups en DR. Gebruik [Azure Backup](https://azure.microsoft.com/services/backup/) of [consistente momentopnamen.](#alternative-solution-consistent-snapshots)

 De volgende tabel is een overzicht van de oplossingen die beschikbaar zijn voor DR.

| Scenario | Automatische replicatie | DR-oplossing |
| --- | --- | --- |
| Premium SSD-schijven | Lokaal ([lokaal redundante opslag](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Managed Disks | Lokaal ([lokaal redundante opslag](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Onbeheerde lokaal redundante opslagschijven | Lokaal ([lokaal redundante opslag](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Niet-beheerde georedundante opslagschijven | Regio overschrijden[(georedundante opslag](../articles/storage/common/storage-redundancy-grs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Consistente momentopnamen](#alternative-solution-consistent-snapshots) |
| Onbeheerde georedundante opslagschijven met leestoegang | Regio overschrijden[(georedundante opslag voor lezen](../articles/storage/common/storage-redundancy.md)toegang) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Consistente momentopnamen](#alternative-solution-consistent-snapshots) |

Hoge beschikbaarheid wordt het best bereikt door beheerde schijven te gebruiken in een beschikbaarheidsset samen met Azure Backup. Als u onbeheerde schijven gebruikt, u Azure Backup nog steeds gebruiken voor DR. Als u Azure Backup niet gebruiken, is het maken van [consistente momentopnamen](#alternative-solution-consistent-snapshots), zoals beschreven in een later gedeelte, een alternatieve oplossing voor back-up en DR.

Uw keuzes voor hoge beschikbaarheid, back-up en DR op toepassings- of infrastructuurniveaus kunnen als volgt worden weergegeven:

| Niveau |   Hoge beschikbaarheid   | Back-up of DR |
| --- | --- | --- |
| Toepassing | SQL Server AlwaysOn | Azure Backup |
| Infrastructuur    | Beschikbaarheidsset  | Georedundante opslag met consistente momentopnamen |

### <a name="using-azure-backup"></a>Azure Backup gebruiken 

[Azure Backup](../articles/backup/backup-azure-vms-introduction.md) kan een back-up maken van uw VM's met Windows of Linux naar de kluis azure recovery services. Het maken van back-ups en het herstellen van bedrijfskritieke gegevens wordt bemoeilijkt door het feit dat er een back-up moet worden genomen van bedrijfskritieke gegevens terwijl de toepassingen die de gegevens produceren, worden uitgevoerd. 

Om dit probleem op te lossen, biedt Azure Backup toepassingsconsistente back-ups voor Microsoft-workloads. Het maakt gebruik van de volume schaduw service om ervoor te zorgen dat de gegevens correct wordt geschreven naar opslag. Voor Linux VM's is de standaard back-upconsistentiemodus bestandsconsistente back-ups, omdat Linux geen functionaliteit heeft die gelijkwaardig is aan de volumeschaduwservice zoals in het geval van Windows. Zie [Toepassingsconsistente back-up van Azure Linux VM's voor Linux-machines.](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)

![Azure-back-upstroom][1]

Wanneer Azure Backup op de geplande tijd een back-uptaak initieert, wordt de back-upextensie die in de VM is geïnstalleerd, geactiveerd om een point-in-time-momentopname te maken. Een momentopname wordt genomen in coördinatie met de volumeschaduwservice om een consistente momentopname van de schijven in de virtuele machine te krijgen zonder deze af te sluiten. De back-upextensie in de VM spoelt alle schrijft door voordat u een consistente momentopname maakt van alle schijven. Na het maken van de momentopname worden de gegevens door Azure Backup overgebracht naar de back-upkluis. Om het back-upproces efficiënter te maken, identificeert en draagt de service alleen de gegevensblokken over die zijn gewijzigd na de laatste back-up.

Als u wilt herstellen, u de beschikbare back-ups bekijken via Azure Backup en vervolgens een herstel starten. U Azure-back-ups maken en herstellen via de [Azure-portal,](https://portal.azure.com/) [met PowerShell](../articles/backup/backup-azure-vms-automation.md)of met behulp van de [Azure CLI](/cli/azure/).

### <a name="steps-to-enable-a-backup"></a>Stappen om een back-up in te schakelen

Gebruik de volgende stappen om back-ups van uw VM's in te schakelen met behulp van de [Azure-portal.](https://portal.azure.com/) Er is enige variatie, afhankelijk van uw exacte scenario. Raadpleeg de [Azure Backup-documentatie](../articles/backup/backup-azure-vms-introduction.md) voor alle details. Azure Backup ondersteunt ook [VM's met beheerde schijven.](https://azure.microsoft.com/blog/azure-managed-disk-backup/)

1.  Maak een kluis voor herstelservices voor een virtuele machine:

    a. Blader in de [Azure-portal](https://portal.azure.com/)door **Alle bronnen** en zoek **vaults van Recovery Services.**

    b. Klik in het menu **Vaults van Recovery Services** op **Toevoegen** en volg de stappen om een nieuwe kluis te maken in dezelfde regio als de vm. Als uw vm zich bijvoorbeeld in de regio West-VS bevindt, kiest u West US voor de kluis.

1.  Controleer de opslagreplicatie voor de nieuw gemaakte kluis. Toegang tot de kluis onder **Vaults van Recovery Services** en ga naar **Properties** > **Backup Configuration** > **Update**. Zorg ervoor dat de **optie voor georedundante opslag** standaard is geselecteerd. Deze optie zorgt ervoor dat uw kluis automatisch wordt gerepliceerd naar een secundair datacenter. Uw kluis in West-VS wordt bijvoorbeeld automatisch gerepliceerd naar Oost-VS.

1.  Configureer het back-upbeleid en selecteer de VM uit dezelfde gebruikersinterface.

1.  Controleer of de back-upagent op de vm is geïnstalleerd. Als uw vm is gemaakt met behulp van een Azure-galerieafbeelding, is de back-upagent al geïnstalleerd. Anders (dat wil zeggen, als u een aangepaste afbeelding gebruikt), gebruikt u de instructies om [de VM-agent op een virtuele machine](../articles/backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent)te installeren.

1.  Nadat de vorige stappen zijn voltooid, wordt de back-up regelmatig uitgevoerd, zoals opgegeven in het back-upbeleid. Indien nodig u de eerste back-up handmatig activeren vanuit het vault-dashboard op de Azure-portal.

Raadpleeg [PowerShell-cmdlets voor HET automatiseren](../articles/backup/backup-azure-vms-automation.md)van Azure Backup voor het automatiseren van Azure Backup met behulp van scripts.

### <a name="steps-for-recovery"></a>Stappen voor herstel

Als u een VM moet repareren of opnieuw opbouwen, u de VM herstellen vanaf een van de back-upherstelpunten in de kluis. Er zijn een paar verschillende opties voor het uitvoeren van het herstel:

-   U een nieuwe virtuele machine maken als point-in-time weergave van uw back-up VM.

-   U de schijven herstellen en vervolgens de sjabloon voor de virtuele machine gebruiken om de herstelde vm aan te passen en opnieuw op te bouwen.

Zie de instructies om [de Azure-portal te gebruiken om virtuele machines te herstellen voor](../articles/backup/backup-azure-arm-restore-vms.md)meer informatie. In dit document worden ook de specifieke stappen uitgelegd voor het herstellen van back-upVM's naar een gekoppeld datacenter met behulp van uw georedundante back-upkluis als er een ramp is in het primaire datacenter. In dat geval gebruikt Azure Backup de Compute-service vanuit het secundaire gebied om de herstelde virtuele machine te maken.

U PowerShell ook gebruiken voor [het maken van een nieuwe virtuele machine van herstelde schijven.](../articles/backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks)

## <a name="alternative-solution-consistent-snapshots"></a>Alternatieve oplossing: Consistente momentopnamen

Als u Azure Backup niet gebruiken, u uw eigen back-upmechanisme implementeren met behulp van momentopnamen. Het maken van consistente momentopnamen voor alle schijven die door een virtuele machine worden gebruikt en deze momentopnamen repliceren naar een andere regio, is ingewikkeld. Daarom beschouwt Azure het gebruik van de back-upservice als een betere optie dan het bouwen van een aangepaste oplossing.

Als u georedundante opslag/georedundante opslag voor leestoegang voor schijven gebruikt, worden momentopnamen automatisch gerepliceerd naar een secundair datacenter. Als u lokaal redundante opslag voor schijven gebruikt, moet u de gegevens zelf repliceren. Zie [Back-ups van niet-beheerde VM-schijven met azure-back-ups met incrementele momentopnamen](../articles/virtual-machines/windows/incremental-snapshots.md)voor meer informatie.

Een momentopname is een weergave van een object op een bepaald tijdstip. Voor een momentopname wordt facturering gemaakt voor de incrementele grootte van de gegevens die worden bevat. Zie [Een blobmomentopname maken](../articles/storage/blobs/storage-blob-snapshots.md)voor meer informatie.

### <a name="create-snapshots-while-the-vm-is-running"></a>Momentopnamen maken terwijl de vm wordt uitgevoerd

Hoewel u op elk gewenst moment een momentopname maken, worden er, als de VM wordt uitgevoerd, nog steeds gegevens naar de schijven gestreamd. De momentopnamen kunnen gedeeltelijke bewerkingen bevatten die tijdens de vlucht waren. Ook als er meerdere schijven bij betrokken zijn, kunnen de momentopnamen van verschillende schijven op verschillende tijdstippen zijn opgetreden. Deze scenario's kunnen ertoe leiden dat de momentopnamen ongecoördineerd zijn. Dit gebrek aan coördinatie is vooral problematisch voor gestreepte volumes waarvan de bestanden kunnen worden beschadigd als er wijzigingen werden aangebracht tijdens de back-up.

Om deze situatie te voorkomen, moet het back-upproces de volgende stappen implementeren:

1.  Bevries alle schijven.

1.  Spoel alle lopende schrijft.

1.  [Maak een blobmomentopname](../articles/storage/blobs/storage-blob-snapshots.md) voor alle schijven.

Sommige Windows-toepassingen, zoals SQL Server, bieden een gecoördineerd back-upmechanisme via een volumeschaduwservice om toepassingsconsistente back-ups te maken. Op Linux, u gebruik maken van een tool zoals *fsfreeze* voor de coördinatie van de schijven. Deze tool biedt bestandsconsistente back-ups, maar geen momentopnamen voor toepassingsconsistente momentopnamen. Dit proces is complex, dus u moet overwegen [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) te gebruiken of een back-upoplossing van derden die deze procedure al implementeert.

Het vorige proces resulteert in een verzameling gecoördineerde momentopnamen voor alle VM-schijven, wat een specifieke point-in-time-weergave van de VM vertegenwoordigt. Dit is een back-upherstelpunt voor de VM. U het proces met geplande intervallen herhalen om periodieke back-ups te maken. Zie [De back-ups kopiëren naar een andere regio](#copy-the-snapshots-to-another-region) voor stappen om de momentopnamen naar een andere regio voor DR te kopiëren.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Momentopnamen maken terwijl de virtuele machine offline is

Een andere optie om consistente back-ups te maken, is het afsluiten van de VM en het maken van blobsnapshots van elke schijf. Het maken van blobsnapshots is eenvoudiger dan het coördineren van snapshots van een lopende VM, maar het vereist een paar minuten downtime.

1. Schakel de VM uit.

1. Maak een momentopname van elke virtuele blob op de harde schijf, die slechts enkele seconden duurt.

    Als u een momentopname wilt maken, u [PowerShell,](../articles/storage/common/storage-powershell-guide-full.md)de [Azure Storage REST API,](https://msdn.microsoft.com/library/azure/ee691971.aspx) [Azure CLI](/cli/azure/)of een van de Azure Storage-clientbibliotheken gebruiken, zoals [de opslagclientbibliotheek voor .NET](https://msdn.microsoft.com/library/azure/hh488361.aspx).

1. Start de VM, waarmee de downtime wordt beëindigd. Meestal is het hele proces binnen een paar minuten voltooid.

Dit proces levert een verzameling consistente momentopnamen voor alle schijven, waardoor een back-up herstelpunt voor de VM.

### <a name="copy-the-snapshots-to-another-region"></a>De momentopnamen naar een andere regio kopiëren

Het maken van de momentopnamen alleen is mogelijk niet voldoende voor DR. U moet de momentopnameback-ups ook repliceren naar een andere regio.

Als u georedundante opslag of georedundante opslag voor leestoegang voor uw schijven gebruikt, worden de momentopnamen automatisch gerepliceerd naar de secundaire regio. Er kan een paar minuten vertraging zijn voor de replicatie. Als het primaire datacenter uitvalt voordat de momentopnamen zijn gerepliceerd, hebt u geen toegang tot de momentopnamen van het secundaire datacenter. De kans hierop is klein.

> [!NOTE]
> Alleen het hebben van de schijven in een geo-redundante opslag of read-access geo-redundante opslag account beschermt de VM niet tegen rampen. U moet ook gecoördineerde momentopnamen maken of Azure Backup gebruiken. Dit is nodig om een VM te herstellen naar een consistente status.

Als u lokaal redundante opslag gebruikt, moet u de momentopnamen direct na het maken van de momentopname naar een ander opslagaccount kopiëren. Het kopieerdoel kan een lokaal redundantopslagaccount in een andere regio zijn, waardoor de kopie zich in een afgelegen regio bevindt. U de momentopname ook kopiëren naar een georedundant-opslagaccount met leestoegang in dezelfde regio. In dit geval wordt de momentopname lui gerepliceerd naar het externe secundaire gebied. Uw back-up is beschermd tegen rampen op de primaire site nadat het kopiëren en de replicatie is voltooid.

Als u uw incrementele momentopnamen voor DR efficiënt wilt kopiëren, controleert u de instructies in [Back-ups van azure-onbeheerde VM-schijven met incrementele momentopnamen.](../articles/virtual-machines/windows/incremental-snapshots.md)

![Back-ups maken van azure-onbeheerde VM-schijven met incrementele momentopnamen][2]

### <a name="recovery-from-snapshots"></a>Herstel van momentopnamen

Als u een momentopname wilt ophalen, kopieert u deze om een nieuwe blob te maken. Als u de momentopname kopieert van het primaire account, u de momentopname kopiëren naar de basisblob van de momentopname. Met dit proces wordt de schijf teruggezet naar de momentopname. Dit proces staat bekend als het promoten van de momentopname. Als u de momentopnameback-back-up kopieert van een secundair account, moet u deze in het geval van een geo-redundante opslagaccount met leestoegang kopiëren naar een primair account. U een momentopname kopiëren [met PowerShell](../articles/storage/common/storage-powershell-guide-full.md) of met het AzCopy-hulpprogramma. Zie [Gegevens overzetten met het azcopy-opdrachtregelhulpprogramma](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)voor meer informatie.

Voor VM's met meerdere schijven moet u alle momentopnamen kopiëren die deel uitmaken van hetzelfde gecoördineerde herstelpunt. Nadat u de momentopnamen hebt gekopieerd naar beschrijfbare VHD-blobs, u de blobs gebruiken om uw VM opnieuw te maken met behulp van de sjabloon voor de VM.

## <a name="other-options"></a>Andere opties

### <a name="sql-server"></a>SQL Server

SQL Server die in een VM wordt uitgevoerd, heeft zijn eigen ingebouwde mogelijkheden om een back-up te maken van uw SQL Server-database naar Azure Blob-opslag of een bestandsshare. Als het opslagaccount georedundante opslag of georedundante opslag voor leestoegang is, hebt u toegang tot deze back-ups in het secundaire datacenter van het opslagaccount in het geval van een ramp, met dezelfde beperkingen als eerder besproken. Zie [Back-ups maken en herstellen voor SQL Server in virtuele Azure-machines voor](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md)meer informatie. Sql [Server AlwaysOn-beschikbaarheidsgroepen](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) kunnen niet alleen een back-up maken en herstellen, maar ook secundaire replica's van databases onderhouden. Dit vermogen vermindert de hersteltijd na noodgevallen sterk.

## <a name="other-considerations"></a>Andere overwegingen

In dit artikel wordt besproken hoe u een back-up maken of snapshots van uw VM's en hun schijven maken om herstel na noodgevallen te ondersteunen en hoe u deze back-ups of momentopnamen gebruiken om uw gegevens te herstellen. Met het Azure Resource Manager-model gebruiken veel mensen sjablonen om hun VM's en andere infrastructuren in Azure te maken. U een sjabloon gebruiken om een VM te maken die elke keer dezelfde configuratie heeft. Als u aangepaste afbeeldingen gebruikt voor het maken van uw VM's, moet u er ook voor zorgen dat uw afbeeldingen worden beschermd met behulp van een georedundant-opslagaccount met leestoegang om ze op te slaan.

Daarom kan uw back-upproces een combinatie zijn van twee dingen:

- Een back-up van de gegevens (schijven).
- Een back-up van de configuratie (sjablonen en aangepaste afbeeldingen).

Afhankelijk van de back-upoptie die u kiest, moet u mogelijk de back-up van zowel de gegevens als de configuratie afhandelen, of de back-upservice kan dat allemaal voor u afhandelen.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Bijlage: Inzicht in de impact van gegevensredundantie

Voor opslagaccounts in Azure zijn er drie soorten gegevensredundantie die u moet overwegen met betrekking tot noodherstel: lokaal redundant, georedundant of geo-redundant met leestoegang. 

Lokaal redundante opslag behoudt drie kopieën van de gegevens in hetzelfde datacenter. Wanneer de VM de gegevens schrijft, worden alle drie de kopieën bijgewerkt voordat het succes wordt geretourneerd naar de beller, zodat u weet dat ze identiek zijn. Uw schijf is beschermd tegen lokale storingen, omdat het onwaarschijnlijk is dat alle drie de kopieën tegelijkertijd worden beïnvloed. In het geval van lokaal redundante opslag is er geen georedundantie, zodat de schijf niet is beschermd tegen catastrofale fouten die een volledig datacenter of opslageenheid kunnen beïnvloeden.

Met georedundante opslag en georedundante opslag voor leestoegang worden drie kopieën van uw gegevens bewaard in het primaire gebied dat door u is geselecteerd. Nog drie exemplaren van uw gegevens worden bewaard in een overeenkomstigsecundair e-werkgebied dat is ingesteld door Azure. Als u bijvoorbeeld gegevens opslaat in West-VS, worden de gegevens gerepliceerd naar Oost-VS. Het bewaren van kopiëren gebeurt asynchroon en er is een kleine vertraging tussen updates voor de primaire en secundaire sites. Replica's van de schijven op de secundaire site zijn consistent op basis van één schijf (met de vertraging), maar replica's van meerdere actieve schijven zijn mogelijk niet gesynchroniseerd met elkaar. Om consistente replica's op meerdere schijven te hebben, zijn consistente momentopnamen nodig.

Het belangrijkste verschil tussen geo-redundante opslag en georedundante opslag met leestoegang is dat u met georedundante opslag met leestoegang de secundaire kopie op elk gewenst moment lezen. Als er een probleem is waardoor de gegevens in de primaire regio ontoegankelijk zijn, stelt het Azure-team alles in het werk om de toegang te herstellen. Terwijl de primaire is uitgeschakeld, als u geo-redundante opslag voor lezen hebt ingeschakeld, hebt u toegang tot de gegevens in het secundaire datacenter. Als u daarom van plan bent om uit de replica te lezen terwijl de primaire niet toegankelijk is, moet de georedundante opslag voor lezen worden overwogen.

Als het een aanzienlijke storing blijkt te zijn, kan het Azure-team een geo-failover activeren en de primaire DNS-vermeldingen wijzigen om naar secundaire opslag te wijzen. Als u op dit moment georedundante opslag of georedundante opslag met leestoegang hebt ingeschakeld, hebt u toegang tot de gegevens in de regio die voorheen secundair was. Met andere woorden, als uw opslagaccount georedundante opslag is en er een probleem is, hebt u alleen toegang tot de secundaire opslag als er een geo-failover is.

Zie [Wat te doen in het geval van een Azure Storage-storing](../articles/storage/common/storage-disaster-recovery-guidance.md) voor meer informatie.

>[!NOTE] 
>Microsoft bepaalt of er een failover optreedt. Failover wordt niet gecontroleerd per opslagaccount, dus het wordt niet bepaald door individuele klanten. Als u noodherstel wilt implementeren voor specifieke opslagaccounts of virtuele machineschijven, moet u de eerder in dit artikel beschreven technieken gebruiken.

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png
