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
ms.openlocfilehash: 9332079cd77c4dcc972059071165ba0631135b5c
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012512"
---
In dit artikel wordt uitgelegd hoe u back-ups en herstel na nood gevallen (DR) van IaaS virtual machines (Vm's) en schijven in azure plant. In dit document worden zowel beheerde als onbeheerde schijven beschreven.

Eerst bieden we de ingebouwde mogelijkheden voor fout tolerantie in het Azure-platform dat bescherming biedt tegen lokale storingen. Vervolgens bespreken we de nood scenario's die niet volledig worden gedekt door de ingebouwde mogelijkheden. Er worden ook verschillende voor beelden van werkbelasting scenario's weer gegeven waarin verschillende back-ups en DR-overwegingen van toepassing kunnen zijn. Vervolgens worden mogelijke oplossingen voor de DR van IaaS-schijven gecontroleerd.

## <a name="introduction"></a>Inleiding

Het Azure-platform gebruikt verschillende methoden voor redundantie en fout tolerantie om klanten te beschermen tegen gelokaliseerde hardwarestoringen. Lokale fouten kunnen problemen bevatten met een Azure Storage Server machine die een deel van de gegevens voor een virtuele schijf of fouten van een SSD of HDD op die server opslaat. Dergelijke defecte hardwareonderdelen kunnen zich voordoen tijdens normale bewerkingen.

Het Azure-platform is zo ontworpen dat deze fouten zich kunnen voordoen. Grote rampen kunnen leiden tot storingen of het ontoegankelijk zijn van veel opslag servers of zelfs voor een heel Data Center. Hoewel uw Vm's en schijven normaal gesp roken worden beschermd tegen gelokaliseerde fouten, zijn er extra stappen nodig om uw workload te beschermen tegen onherstelbare problemen die betrekking hebben op de hele regio, zoals een zware ramp, die van invloed kan zijn op uw virtuele machine en schijven.

Naast de mogelijkheid van platform storingen, kunnen er problemen optreden met een klant toepassing of-gegevens. Een nieuwe versie van uw toepassing kan bijvoorbeeld per ongeluk een wijziging aanbrengen in de gegevens die ervoor zorgen dat deze wordt verbroken. In dat geval wilt u mogelijk de toepassing en de gegevens herstellen naar een eerdere versie die de laatste bekende juiste status bevat. Hiervoor moeten reguliere back-ups worden onderhouden.

Voor regionale nood herstel moet u een back-up maken van uw IaaS VM-schijven naar een andere regio.

Voordat we gaan kijken naar back-ups en DR-opties, gaan we enkele methoden samen vatting die beschikbaar zijn voor het verwerken van gelokaliseerde fouten.

### <a name="azure-iaas-resiliency"></a>Tolerantie van Azure IaaS

*Tolerantie* verwijst naar de tolerantie voor normale storingen die zich voordoen in hardware-onderdelen. Tolerantie is de mogelijkheid om fouten te herstellen en verder te werken. Het is niet mogelijk om storingen te voor komen, maar op een manier te reageren op fouten waardoor downtime of gegevens verlies wordt voor komen. Het doel van flexibiliteit is om de toepassing na een storing weer volledig te laten functioneren. Virtuele machines en schijven van Azure zijn ontworpen om te worden gebruikt voor veelvoorkomende hardwarefouten. Laten we eens kijken hoe het Azure IaaS-platform deze tolerantie biedt.

Een virtuele machine bestaat voornamelijk uit twee delen: een Compute Server en de permanente schijven. Beide beïnvloeden de fout tolerantie van een virtuele machine.

Als de Azure Compute-hostserver die uw virtuele machine bevat een hardwarestoring heeft, wat zelden voor komt, is Azure ontworpen om de VM automatisch te herstellen op een andere server. Als dit scenario wordt weer gegeven, wordt de computer opnieuw opgestart en wordt de virtuele machine na enige tijd een back-up gemaakt. Azure detecteert dergelijke hardwarestoringen automatisch en voert herstel bewerkingen uit om ervoor te zorgen dat de virtuele machine van de klant zo snel mogelijk beschikbaar is.

Met betrekking tot IaaS-schijven is de duurzaamheid van gegevens essentieel voor een permanent opslag platform. Azure-klanten hebben belang rijke zakelijke toepassingen die worden uitgevoerd op IaaS en ze zijn afhankelijk van de persistentie van de gegevens. Azure Designs Protection voor deze IaaS-schijven, met drie redundante kopieën van de gegevens die lokaal zijn opgeslagen. Deze kopieën bieden een hoge duurzaamheid tegen lokale storingen. Als een van de hardwareonderdelen van uw schijf uitvalt, wordt uw virtuele machine niet beïnvloed, omdat er twee extra kopieën zijn om schijf aanvragen te ondersteunen. Het werkt prima, zelfs als twee verschillende hardwareonderdelen die een schijf kunnen ondersteunen, op hetzelfde moment worden uitgevoerd (wat zelden voor komt). 

Om ervoor te zorgen dat u altijd drie replica's bijhoudt, wordt Azure Storage automatisch een nieuwe kopie van de gegevens op de achtergrond geïnitieerd als een van de drie kopieën niet meer beschikbaar is. Daarom is het niet nodig om RAID te gebruiken met Azure-schijven voor fout tolerantie. Een eenvoudige RAID 0-configuratie moet voldoende zijn voor het verwijderen van de schijven, indien nodig, om grotere volumes te maken.

Vanwege deze architectuur heeft Azure voortdurend duurzaamheid op bedrijfs niveau geboden voor IaaS-schijven, met een toonaangevende uitval percentage van 0 procent [per jaar](https://en.wikipedia.org/wiki/Annualized_failure_rate).

Gelokaliseerde hardwarefouten op de compute-host of in het opslag platform kunnen soms leiden tot de tijdelijke niet-beschik baarheid van de virtuele machine die wordt gedekt door de [Azure Sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/) voor VM-Beschik baarheid. Azure biedt ook een toonaangevende SLA voor afzonderlijke VM-instanties die gebruikmaken van Azure Premium Ssd's.

Om de werk belastingen van toepassingen te beschermen tegen downtime vanwege de tijdelijke niet-beschik baarheid van een schijf of virtuele machine, kunnen klanten [beschikbaarheids sets](../articles/virtual-machines/windows/manage-availability.md)gebruiken. Twee of meer virtuele machines in een beschikbaarheidsset bieden redundantie voor de toepassing. Azure maakt deze Vm's en schijven vervolgens in afzonderlijke fout domeinen met verschillende energie-, netwerk-en Server onderdelen.

Vanwege deze afzonderlijke fout domeinen hebben gelokaliseerde hardwarestoringen doorgaans geen invloed op meerdere Vm's in de set. Als u afzonderlijke fout domeinen hebt, beschikt u over hoge Beschik baarheid voor uw toepassing. Het is een goede gewoonte om beschikbaarheids sets te gebruiken wanneer hoge Beschik baarheid is vereist. In de volgende sectie wordt het aspect nood herstel beschreven.

### <a name="backup-and-disaster-recovery"></a>Back-up en herstel na noodgeval

Herstel na nood gevallen is de mogelijkheid om van zeldzame, maar grote incidenten te herstellen. Deze incidenten omvatten niet-tijdelijke, grootschalige storingen, zoals service-onderbrekingen die van invloed zijn op een hele regio. Herstel na nood gevallen omvat gegevens back-up en-archivering en kan hand matige interventie zijn, zoals het herstellen van een Data Base uit een back-up.

De ingebouwde beveiliging van het Azure-platform tegen gelokaliseerde fouten kan de Vm's/schijven mogelijk niet volledig beveiligen als een grote nood geval grote storingen veroorzaakt. Deze grootschalige storingen omvatten fatale gebeurtenissen, zoals wanneer een Data Center wordt bereikt door een orkaan, aard beving of vuur, of als er een grootschalige fout is opgetreden in de hardware-eenheid. Daarnaast kunnen er fouten optreden vanwege toepassings-of gegevens problemen.

Als u uw IaaS-workloads wilt beveiligen tegen storingen, moet u een planning maken voor redundantie en back-ups maken om herstel te kunnen inschakelen. Voor herstel na nood gevallen moet u een back-up van een andere geografische locatie maken vanaf de primaire site. Deze aanpak zorgt ervoor dat de back-up niet wordt beïnvloed door dezelfde gebeurtenis die de VM of schijven oorspronkelijk beïnvloedde. Zie [herstel na nood gevallen voor Azure-toepassingen](/azure/architecture/resiliency/disaster-recovery-azure-applications)voor meer informatie.

Uw DR-overwegingen kunnen de volgende aspecten bevatten:

- Hoge Beschik baarheid: de mogelijkheid van de toepassing om te blijven functioneren met een goede status, zonder aanzienlijke uitval tijd. Als de status *in orde*is, betekent dit dat de toepassing reageert en kunnen gebruikers verbinding maken met de toepassing en ermee werken. Bepaalde essentiële toepassingen en data bases kunnen vereist zijn om altijd beschikbaar te zijn, zelfs wanneer er fouten zijn in het platform. Voor deze werk belastingen moet u mogelijk ook de redundantie voor de toepassing en de gegevens plannen.

- Duurzaamheid van gegevens: in sommige gevallen zorgt de belangrijkste overweging ervoor dat de gegevens behouden blijven als er een nood geval is. Daarom hebt u mogelijk een back-up nodig van uw gegevens in een andere site. Voor dergelijke werk belastingen hebt u mogelijk niet de volledige redundantie voor de toepassing nodig, maar alleen een reguliere back-up van de schijven.

## <a name="backup-and-dr-scenarios"></a>Back-ups en nood herstel scenario's

Bekijk enkele typische voor beelden van de werkbelasting scenario's voor toepassingen en de overwegingen voor het plannen van nood herstel.

### <a name="scenario-1-major-database-solutions"></a>Scenario 1: belang rijke database oplossingen

Overweeg een productie database server, zoals SQL Server of Oracle, die hoge Beschik baarheid kan ondersteunen. Kritieke productie toepassingen en-gebruikers zijn afhankelijk van deze data base. Het nood herstel plan voor dit systeem moet mogelijk de volgende vereisten ondersteunen:

- De gegevens moeten worden beveiligd en hersteld.
- De server moet beschikbaar zijn voor gebruik.

Voor het nood herstel plan moet mogelijk een replica van de data base in een andere regio worden bewaard als back-up. Afhankelijk van de vereisten voor Server beschikbaarheid en gegevens herstel, kan de oplossing variëren van een Active-Active-of Active-passieve replica site om periodiek offline back-ups van de gegevens te maken. Relationele data bases, zoals SQL Server en Oracle, bieden verschillende opties voor replicatie. Gebruik [SQL Server AlwaysOn-beschikbaarheidsgroepen](https://msdn.microsoft.com/library/hh510230.aspx) voor maximale Beschik baarheid voor SQL Server.

NoSQL-data bases, zoals MongoDB, ondersteunen ook [replica's](https://docs.mongodb.com/manual/replication/) voor redundantie. De replica's voor hoge Beschik baarheid worden gebruikt.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Scenario 2: een cluster met redundante Vm's

Denk na over een werk belasting die wordt afgehandeld door een cluster met virtuele machines die redundantie en taak verdeling bieden. Een voor beeld is een Cassandra-cluster dat in een regio is geïmplementeerd. Dit type architectuur biedt al een hoge mate van redundantie binnen die regio. Als u de werk belasting van een storing op een regionaal niveau wilt beveiligen, kunt u overwegen om het cluster over twee regio's te verspreiden of periodieke back-ups naar een andere regio te maken.

### <a name="scenario-3-iaas-application-workload"></a>Scenario 3: werk belasting IaaS toepassing

Laten we eens kijken naar de workload van de IaaS-toepassing. Deze toepassing kan bijvoorbeeld een typische productiewerk belasting zijn die wordt uitgevoerd op een virtuele machine van Azure. Dit kan een webserver zijn of een bestands server met de inhoud en andere bronnen van een site. Het kan ook een aangepaste bedrijfs toepassing zijn die wordt uitgevoerd op een virtuele machine die de gegevens, bronnen en toepassings status op de VM-schijven heeft opgeslagen. In dit geval is het belang rijk om ervoor te zorgen dat u regel matig back-ups maakt. De back-upfrequentie moet gebaseerd zijn op de aard van de VM-workload. Als de toepassing bijvoorbeeld elke dag wordt uitgevoerd en gegevens wijzigt, moet de back-up elk uur worden gemaakt.

Een ander voor beeld is een rapport server die gegevens ophaalt uit andere bronnen en geaggregeerde rapporten genereert. Het verlies van deze virtuele machine of schijven kan leiden tot verlies van de rapporten. Het is echter mogelijk het rapportage proces opnieuw uit te voeren en de uitvoer opnieuw te genereren. In dat geval hebt u geen gegevens verlies meer, zelfs als de rapport server met een nood geval wordt bereikt. Als gevolg hiervan is het mogelijk dat u een hoger tolerantie niveau hebt voor het verliezen van een deel van de gegevens op de rapport server. In dat geval zijn minder frequente back-ups een optie om de kosten te verlagen.

### <a name="scenario-4-iaas-application-data-issues"></a>Scenario 4: problemen met IaaS-toepassings gegevens

IaaS toepassings gegevens problemen zijn een andere mogelijkheid. Houd rekening met een toepassing die essentiële commerciële gegevens berekent, onderhoudt en gebruikt, zoals prijs informatie. Een nieuwe versie van uw toepassing had een software fout die de prijzen onjuist heeft berekend en de bestaande commerce gegevens die door het platform worden geleverd, hebben beschadigd. Hier volgt de beste actie om terug te keren naar de eerdere versie van de toepassing en de gegevens. U kunt dit doen door periodieke back-ups van uw systeem te maken.

## <a name="disaster-recovery-solution-azure-backup"></a>Oplossing voor herstel na nood gevallen: Azure Backup 

[Azure backup](https://azure.microsoft.com/services/backup/) wordt gebruikt voor back-ups en Dr. het werkt met [beheerde schijven](../articles/virtual-machines/windows/managed-disks-overview.md) en niet-beheerde schijven. U kunt een back-uptaak maken met back-ups op basis van tijd, eenvoudig herstel van de virtuele machine en het Bewaar beleid voor back-ups.

Als u [Premium ssd's](../articles/virtual-machines/windows/disks-types.md), [Managed disks](../articles/virtual-machines/windows/managed-disks-overview.md)of andere schijf typen met de [lokaal redundante opslag](../articles/storage/common/storage-redundancy-lrs.md) optie gebruikt, is het vooral belang rijk om periodieke back-ups van Dr te maken. Azure Backup slaat de gegevens op in uw Recovery Services-kluis voor lange termijn retentie. Kies de optie [geo-redundante opslag](../articles/storage/common/storage-redundancy-grs.md) voor de back-up Recovery Services-kluis. Deze optie zorgt ervoor dat back-ups worden gerepliceerd naar een andere Azure-regio voor beveiliging tegen regionale rampen.

Voor niet-beheerde schijven kunt u het lokaal redundante opslag type gebruiken voor IaaS-schijven, maar moet Azure Backup worden ingeschakeld met de geo-redundante opslag optie voor de Recovery Services-kluis.

> [!NOTE]
> Als u de geo [-redundante opslag](../articles/storage/common/storage-redundancy-grs.md) of [geografisch redundante opslag optie met lees toegang](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) voor uw onbeheerde schijven gebruikt, hebt u nog steeds consistente moment opnamen nodig voor back-up en Dr. Gebruik een [Azure backup](https://azure.microsoft.com/services/backup/) of [consistente moment opnamen](#alternative-solution-consistent-snapshots).

 De volgende tabel bevat een samen vatting van de oplossingen die beschikbaar zijn voor DR.

| Scenario | Automatische replicatie | Oplossing voor nood herstel |
| --- | --- | --- |
| Premium-SSD schijven | Lokaal ([lokaal redundante opslag](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Managed Disks | Lokaal ([lokaal redundante opslag](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Onbeheerde lokaal redundante opslag schijven | Lokaal ([lokaal redundante opslag](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Niet-beheerde geo-redundante opslag schijven | Kruis regio ([geografisch redundante opslag](../articles/storage/common/storage-redundancy-grs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Consistente moment opnamen](#alternative-solution-consistent-snapshots) |
| Niet-beheerde geografisch redundante opslag schijven met lees toegang | Kruis regio ([geografisch redundante opslag met lees toegang](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Consistente moment opnamen](#alternative-solution-consistent-snapshots) |

Hoge Beschik baarheid is het beste te voldoen aan de hand van beheerde schijven in een beschikbaarheidsset samen met Azure Backup. Als u onbeheerde schijven gebruikt, kunt u nog steeds Azure Backup gebruiken voor DR. Als u Azure Backup niet kunt gebruiken, is het maken van [consistente moment opnamen](#alternative-solution-consistent-snapshots), zoals beschreven in een later gedeelte, een alternatieve oplossing voor back-up en Dr.

Uw keuzes voor hoge Beschik baarheid, back-up en DR op toepassings-of infrastructuur niveau kunnen als volgt worden weer gegeven:

| Niveau |   Hoge beschikbaarheid   | Back-up of DR |
| --- | --- | --- |
| Toepassing | SQL Server AlwaysOn | Azure Backup |
| Infrastructuur    | Beschikbaarheidsset  | Geografisch redundante opslag met consistente moment opnamen |

### <a name="using-azure-backup"></a>Azure Backup gebruiken 

[Azure backup](../articles/backup/backup-azure-vms-introduction.md) kunt een back-up maken van uw virtuele machines met Windows of Linux naar de Azure Recovery Services-kluis. Het maken van een back-up en het herstellen van bedrijfskritische gegevens is gecompliceerd door het feit dat er een back-up van bedrijfs kritieke gegevens moet worden gemaakt terwijl de toepassingen die de gegevens produceren, worden uitgevoerd. 

Azure Backup biedt toepassings consistente back-ups voor micro soft-workloads om dit probleem op te lossen. De Volume Shadow-service wordt gebruikt om ervoor te zorgen dat de gegevens correct naar de opslag worden geschreven. Voor virtuele Linux-machines zijn de standaard consistentie van de back-up van bestands consistente back-ups, omdat Linux geen functionaliteit heeft die gelijk is aan de Volume Shadow-Service, zoals in het geval van Windows. Voor Linux-machines raadpleegt u [toepassings consistente back-ups van virtuele machines van Azure Linux](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

![Azure Backup stroom][1]

Wanneer Azure Backup op het geplande tijdstip een back-uptaak initieert, wordt de back-upextensie die in de virtuele machine is geïnstalleerd, geactiveerd om een moment opname van een bepaald tijdstip te maken. Er wordt een moment opname gemaakt in combi natie met de Volume Shadow-service om een consistente moment opname van de schijven op de virtuele machine op te halen zonder dat deze wordt afgesloten. Met de back-upextensie in de virtuele machine worden alle schrijf bewerkingen verwijderd voordat een consistente moment opname van alle schijven wordt gemaakt. Na het maken van de moment opname worden de gegevens overgedragen door Azure Backup naar de back-upkluis. Om het back-upproces efficiënter te maken, identificeert en verzendt de service alleen de gegevens blokken die zijn gewijzigd na de laatste back-up.

Als u wilt herstellen, kunt u de beschik bare back-ups weer geven via Azure Backup en vervolgens een terugzet bewerking starten. U kunt Azure-back-ups maken en herstellen via de [Azure Portal](https://portal.azure.com/), met [behulp van Power shell](../articles/backup/backup-azure-vms-automation.md)of met behulp van de [Azure cli](/cli/azure/).

### <a name="steps-to-enable-a-backup"></a>Stappen voor het inschakelen van een back-up

Gebruik de volgende stappen om back-ups van uw Vm's in te scha kelen met behulp van de [Azure Portal](https://portal.azure.com/). Er zijn enkele verschillen, afhankelijk van uw exacte scenario. Raadpleeg de documentatie van [Azure backup](../articles/backup/backup-azure-vms-introduction.md) voor volledige informatie. Azure Backup [biedt ook ondersteuning voor virtuele machines met beheerde schijven](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Een Recovery Services-kluis maken voor een VM:

    a. Blader in het [Azure Portal](https://portal.azure.com/)door **alle resources** en zoek **Recovery Services-kluizen**.

    b. Klik in het menu **Recovery Services kluizen** op **toevoegen** en volg de stappen voor het maken van een nieuwe kluis in dezelfde regio als de virtuele machine. Als uw virtuele machine zich bijvoorbeeld in de regio vs West bevindt, kiest u voor de kluis West US.

1.  Controleer de opslag replicatie voor de zojuist gemaakte kluis. Open de kluis onder **Recovery Services kluizen** en ga naar **Eigenschappen** > **back-upconfiguratie** > **Update**. Zorg ervoor dat de optie **geo-redundante opslag** is standaard geselecteerd. Deze optie zorgt ervoor dat uw kluis automatisch wordt gerepliceerd naar een secundair Data Center. Uw kluis in West-VS wordt bijvoorbeeld automatisch gerepliceerd naar VS-Oost.

1.  Configureer het back-upbeleid en selecteer de virtuele machine in dezelfde gebruikers interface.

1.  Zorg ervoor dat de back-upagent is geïnstalleerd op de virtuele machine. Als uw virtuele machine is gemaakt met behulp van een Azure Gallery-afbeelding, is de back-upagent al geïnstalleerd. Als dat niet het geval is, gebruikt u de instructies voor [het installeren van de VM-agent op een virtuele machine](../articles/backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent), als u een aangepaste installatie kopie gebruikt.

1.  Zorg ervoor dat de virtuele machine verbinding maakt met het netwerk voor de back-upservice. Volg de instructies voor de [netwerk verbinding](../articles/backup/backup-azure-arm-vms-prepare.md#establish-network-connectivity).

1.  Nadat de vorige stappen zijn voltooid, wordt de back-up met regel matige intervallen uitgevoerd zoals opgegeven in het back-upbeleid. Als dat nodig is, kunt u de eerste back-up hand matig activeren vanuit het kluis dashboard op de Azure Portal.

Raadpleeg de [Power shell-cmdlets voor back-up van vm's](../articles/backup/backup-azure-vms-automation.md)voor het automatiseren van Azure backup met behulp van scripts.

### <a name="steps-for-recovery"></a>Stappen voor herstel

Als u een virtuele machine moet herstellen of opnieuw wilt bouwen, kunt u de VM herstellen vanaf een van de back-upherstel punten in de kluis. Er zijn verschillende opties voor het uitvoeren van het herstel:

-   U kunt een nieuwe virtuele machine maken als een tijdgebonden weer gave van de back-up van de virtuele machine.

-   U kunt de schijven herstellen en vervolgens de sjabloon voor de virtuele machine gebruiken om de herstelde VM aan te passen en opnieuw op te bouwen.

Zie de instructies voor [het gebruik van de Azure portal voor het herstellen van virtuele machines](../articles/backup/backup-azure-arm-restore-vms.md)voor meer informatie. In dit document worden ook de specifieke stappen beschreven voor het herstellen van back-ups van virtuele machines naar een gekoppeld Data Center met behulp van uw Geo-redundante back-upkluis als er sprake is van een nood geval op het primaire Data Center. In dat geval gebruikt Azure Backup de compute-service van de secundaire regio om de herstelde virtuele machine te maken.

U kunt Power shell ook gebruiken voor [het maken van een nieuwe VM op basis van herstelde schijven](../articles/backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Alternatieve oplossing: consistente moment opnamen

Als Azure Backup niet kan worden gebruikt, kunt u uw eigen back-upmechanisme implementeren met behulp van moment opnamen. Het maken van consistente moment opnamen voor alle schijven die worden gebruikt door een virtuele machine en het repliceren van die moment opnamen naar een andere regio is gecompliceerd. Daarom wordt het gebruik van de back-upservice in azure beschouwd als een betere optie dan het bouwen van een aangepaste oplossing.

Als u geografisch redundante opslag/geo-redundante opslag met lees toegang gebruikt voor schijven, worden moment opnamen automatisch gerepliceerd naar een secundair Data Center. Als u lokaal redundante opslag voor schijven gebruikt, moet u de gegevens zelf repliceren. Zie [back-ups maken van Azure-niet-beheerde VM-schijven met incrementele moment opnamen](../articles/virtual-machines/windows/incremental-snapshots.md)voor meer informatie.

Een moment opname is een representatie van een object op een bepaald moment. Een moment opname wordt gefactureerd voor de incrementele grootte van de gegevens die ze bevatten. Zie [een BLOB-moment opname maken](../articles/storage/blobs/storage-blob-snapshots.md)voor meer informatie.

### <a name="create-snapshots-while-the-vm-is-running"></a>Moment opnamen maken terwijl de virtuele machine wordt uitgevoerd

Hoewel u op elk gewenst moment een moment opname kunt maken, worden er nog steeds gegevens gestreamd naar de schijven als de virtuele machine wordt uitgevoerd. De moment opnamen kunnen gedeeltelijke bewerkingen bevatten die zich in de vlucht bevonden. Als er meerdere schijven betrokken zijn, kunnen de moment opnamen van verschillende schijven op verschillende tijdstippen hebben plaatsgevonden. Deze scenario's kunnen ertoe leiden dat de moment opnamen ongecoördineerd zijn. Dit gebrek aan Coordination is vooral problematisch voor striped volumes waarvan de bestanden beschadigd kunnen raken als er wijzigingen zijn aangebracht tijdens het maken van de back-up.

Om deze situatie te voor komen, moet het back-upproces de volgende stappen implementeren:

1.  Alle schijven blok keren.

1.  Alle in behandeling zijnde schrijf bewerkingen leegmaken.

1.  [Maak een BLOB-moment opname](../articles/storage/blobs/storage-blob-snapshots.md) voor alle schijven.

Sommige Windows-toepassingen, zoals SQL Server, bieden een gecoördineerd back-upmechanisme via een Volume Shadow-service om toepassings consistente back-ups te maken. In Linux kunt u een hulp programma zoals *fsfreeze* gebruiken voor het coördineren van de schijven. Dit hulp programma biedt bestands consistente back-ups, maar niet toepassings consistente moment opnamen. Dit proces is complex, dus u kunt overwegen [Azure backup](../articles/backup/backup-azure-vms-introduction.md) of een back-upoplossing van derden te gebruiken die deze procedure al implementeert.

Het vorige proces resulteert in een verzameling gecoördineerde moment opnamen voor alle VM-schijven, die een specifieke tijdgebonden weer gave van de virtuele machine vertegenwoordigen. Dit is een herstel punt voor back-ups voor de virtuele machine. U kunt het proces op geplande intervallen herhalen om periodieke back-ups te maken. Zie [de back-ups naar een andere regio kopiëren](#copy-the-snapshots-to-another-region) voor de stappen voor het kopiëren van de moment opnamen naar een andere regio voor Dr.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Moment opnamen maken terwijl de virtuele machine offline is

Een andere optie voor het maken van consistente back-ups is het afsluiten van de virtuele machine en het nemen van BLOB-moment opnamen van elke schijf. Het maken van BLOB-moment opnamen is gemakkelijker dan het coördineren van moment opnamen van een actieve virtuele machine, maar het vergt enkele minuten uitval tijd.

1. Sluit de virtuele machine af.

1. Maak een moment opname van elke virtuele harde schijf-blob, die slechts een paar seconden in beslag neemt.

    Als u een moment opname wilt maken, kunt u [Power shell](../articles/storage/common/storage-powershell-guide-full.md), de [Azure Storage rest API](https://msdn.microsoft.com/library/azure/ee691971.aspx), [Azure CLI](/cli/azure/)of een van de Azure Storage-client bibliotheken gebruiken, zoals [de Storage-client bibliotheek voor .net](https://msdn.microsoft.com/library/azure/hh488361.aspx).

1. Start de virtuele machine, waardoor de uitval tijd wordt beëindigd. Normaal gesp roken eindigt het hele proces binnen een paar minuten.

Dit proces levert een verzameling van consistente moment opnamen voor alle schijven, zodat er een back-upherstel punt voor de virtuele machine wordt geboden.

### <a name="copy-the-snapshots-to-another-region"></a>De moment opnamen kopiëren naar een andere regio

Het is mogelijk dat er voor DR geen moment opnamen worden gemaakt. U moet ook de back-ups van de moment opname repliceren naar een andere regio.

Als u geografisch redundante opslag of geografisch redundante opslag met lees toegang voor uw schijven gebruikt, worden de moment opnamen automatisch gerepliceerd naar de secundaire regio. Er kan een paar minuten duren voordat de replicatie is uitgevoerd. Als het primaire Data Center verloopt voordat de moment opnamen zijn gerepliceerd, hebt u geen toegang tot de moment opnamen van het secundaire Data Center. De kans op dit is klein.

> [!NOTE]
> Alleen de schijven in een geografisch redundante opslag of geografisch redundant opslag account met lees toegang beveiligt de virtuele machine niet tegen rampen. U moet ook gecoördineerde moment opnamen maken of Azure Backup gebruiken. Dit is vereist om een virtuele machine te herstellen naar een consistente status.

Als u lokaal redundante opslag gebruikt, moet u de moment opnamen direct na het maken van de moment opname naar een ander opslag account kopiëren. Het Kopieer doel is mogelijk een lokaal redundant opslag account in een andere regio, wat resulteert in de kopie in een externe regio. U kunt de moment opname ook kopiëren naar een geografisch redundant opslag account met lees toegang in dezelfde regio. In dit geval wordt de moment opname vertraagd gerepliceerd naar de externe secundaire regio. Uw back-up wordt beveiligd tegen rampen op de primaire site nadat het kopiëren en repliceren is voltooid.

Lees de instructies in [een back-up maken van Azure unmanaged VM-schijven met incrementele moment opnamen](../articles/virtual-machines/windows/incremental-snapshots.md)om uw incrementele moment opnamen te kopiëren voor efficiënt Dr.

![Back-ups maken van Azure unmanaged VM-schijven met incrementele moment opnamen][2]

### <a name="recovery-from-snapshots"></a>Herstel van moment opnamen

Als u een moment opname wilt ophalen, moet u deze kopiëren om een nieuwe BLOB te maken. Als u de moment opname van het primaire account kopieert, kunt u de moment opname kopiëren naar de basis-blob van de moment opname. Met dit proces wordt de schijf teruggezet naar de moment opname. Dit proces staat bekend als het promo veren van de moment opname. Als u de back-upmomentopname van een secundair account kopieert, moet u deze naar een primair account kopiëren in het geval van een geografisch redundant opslag account met lees toegang. U kunt een moment opname kopiëren met behulp van [Power shell](../articles/storage/common/storage-powershell-guide-full.md) of met het hulp programma AzCopy. Zie [gegevens overdragen met het opdracht regel programma AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)voor meer informatie.

Voor virtuele machines met meerdere schijven moet u alle moment opnamen kopiëren die deel uitmaken van hetzelfde gecoördineerde herstel punt. Nadat u de moment opnamen naar Beschrijf bare VHD-blobs hebt gekopieerd, kunt u de blobs gebruiken om de virtuele machine opnieuw te maken met behulp van de sjabloon voor de virtuele machine.

## <a name="other-options"></a>Andere opties

### <a name="sql-server"></a>SQL Server

SQL Server die in een VM worden uitgevoerd, heeft zijn eigen ingebouwde mogelijkheden om een back-up te maken van uw SQL Server-Data Base naar Azure Blob-opslag of een bestands share. Als het opslag account geografisch redundante opslag of geografisch redundante opslag met lees toegang is, hebt u toegang tot de back-ups in het secundaire Data Center van het opslag account in het geval van een ramp, met dezelfde beperkingen als eerder besproken. Zie [back-up en herstel voor SQL Server in azure virtual machines](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md)voor meer informatie. [SQL Server AlwaysOn-beschikbaarheids groepen](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) kunnen niet alleen back-ups maken en herstellen, maar ook secundaire replica's van data bases. Deze mogelijkheid vermindert de nood herstel tijd aanzienlijk.

## <a name="other-considerations"></a>Andere overwegingen

In dit artikel wordt beschreven hoe u back-ups maakt of moment opnamen van uw virtuele machines en hun schijven kunt maken ter ondersteuning van herstel na nood gevallen en hoe u deze back-ups of moment opnamen kunt gebruiken om uw gegevens te herstellen. Met het Azure Resource Manager model gebruiken veel mensen sjablonen om hun Vm's en andere infra structuren in azure te maken. U kunt een sjabloon gebruiken om een VM te maken die elke keer dezelfde configuratie heeft. Als u aangepaste installatie kopieën gebruikt voor het maken van uw Vm's, moet u er ook voor zorgen dat uw installatie kopieën worden beveiligd met een geografisch redundant opslag account met lees toegang om deze op te slaan.

Daarom kan uw back-upproces een combi natie van twee dingen zijn:

- Maak een back-up van de gegevens (schijven).
- Maak een back-up van de configuratie (sjablonen en aangepaste installatie kopieën).

Afhankelijk van de back-upoptie die u kiest, moet u mogelijk de back-up van zowel de gegevens als de configuratie afhandelen, of kan de back-upservice allemaal voor u afhandelen.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Bijlage: informatie over de impact van gegevens redundantie

Voor opslag accounts in azure zijn er drie soorten gegevens redundantie voor herstel na nood gevallen: lokaal redundant, geografisch redundant of geografisch redundant met lees toegang. 

Lokaal redundante opslag behoudt drie kopieën van de gegevens in hetzelfde Data Center. Wanneer de virtuele machine de gegevens schrijft, worden alle drie de kopieën bijgewerkt voordat het succes wordt geretourneerd aan de aanroeper, zodat u weet dat ze identiek zijn. Uw schijf wordt beschermd tegen lokale storingen, omdat het onwaarschijnlijk is dat alle drie de kopieën op hetzelfde moment worden beïnvloed. In het geval van lokaal redundante opslag is er geen geo-redundantie, waardoor de schijf niet wordt beschermd tegen onherstelbare storingen die van invloed kunnen zijn op een volledig Data Center of opslag eenheid.

Met geografisch redundante opslag en geografisch redundante opslag met lees toegang worden drie kopieën van uw gegevens bewaard in de primaire regio die door u is geselecteerd. Er worden nog drie kopieën van uw gegevens bewaard in een bijbehorende secundaire regio die is ingesteld door Azure. Als u bijvoorbeeld gegevens in VS-West opslaat, worden de gegevens gerepliceerd naar VS-Oost. Het bewaren van kopieën wordt asynchroon uitgevoerd en er is een kleine vertraging tussen updates op de primaire en secundaire sites. Replica's van de schijven op de secundaire site zijn consistent per schijf (met de vertraging), maar replica's van meerdere actieve schijven zijn mogelijk niet synchroon met elkaar. Om consistente replica's op meerdere schijven te hebben, zijn consistente moment opnamen vereist.

Het belangrijkste verschil tussen geografisch redundante opslag en geografisch redundante opslag met lees toegang is dat met geografisch redundante opslag met lees toegang. u kunt de secundaire kopie op elk gewenst moment lezen. Als er een probleem is met het weer geven van de gegevens in de primaire regio die ontoegankelijk is, maakt het Azure-team elke moeite om de toegang te herstellen. Als u geografisch redundante opslag met lees toegang hebt ingeschakeld, kunt u de primaire gegevens bekijken in het secundaire Data Center. Als u van plan bent om de replica te lezen terwijl de primaire ontoegankelijk is, moet u rekening houden met geografisch redundante opslag met lees toegang.

Als er een aanzienlijke storing optreedt, kan het Azure-team een geo-failover activeren en de primaire DNS-vermeldingen wijzigen om naar de secundaire opslag te verwijzen. Als u op dit punt geografisch redundante opslag of geografisch redundante opslag met lees toegang hebt ingeschakeld, kunt u toegang krijgen tot de gegevens in de regio die als secundaire is gebruikt. Met andere woorden, als uw opslag account geo-redundante opslag is en er een probleem is, hebt u alleen toegang tot de secundaire opslag als er sprake is van een geo-failover.

Zie [wat te doen als er een Azure Storage storing optreedt](../articles/storage/common/storage-disaster-recovery-guidance.md)voor meer informatie.

>[!NOTE] 
>Micro soft bepaalt of een failover wordt uitgevoerd. Failover wordt niet beheerd per opslag account en wordt dus niet door afzonderlijke klanten bepaald. Als u herstel na nood gevallen voor specifieke opslag accounts of virtuele-machine schijven wilt implementeren, moet u de technieken gebruiken die eerder in dit artikel zijn beschreven.

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png
