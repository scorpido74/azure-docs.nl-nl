---
title: Zoeken naar foutal e-pool- en knooppuntfouten - Azure Batch
description: Dit artikel behandelt de achtergrondbewerkingen die kunnen optreden, samen met fouten om te controleren op en hoe deze te voorkomen bij het maken van pools en knooppunten.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: conceptual
ms.openlocfilehash: a68d812a044c776819d169d5bf179f011d06390f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472942"
---
# <a name="check-for-pool-and-node-errors"></a>Controleren op foutal e-pool- en knooppuntfouten

Wanneer u Azure Batch-pools maakt en beheert, worden sommige bewerkingen onmiddellijk uitgevoerd. Sommige bewerkingen zijn echter asynchroon en worden op de achtergrond uitgevoerd, waarbij enkele minuten nodig zijn.

Het detecteren van fouten voor bewerkingen die onmiddellijk plaatsvinden, is eenvoudig omdat eventuele fouten onmiddellijk worden geretourneerd door de API, CLI of UI.

In dit artikel worden de achtergrondbewerkingen die kunnen plaatsvinden voor groepen en poolknooppunten. Het geeft aan hoe u fouten detecteren en voorkomen.

## <a name="pool-errors"></a>Poolfouten

### <a name="resize-timeout-or-failure"></a>Het formaat van een time-out of fout wijzigen

Wanneer u een nieuwe groep maakt of de grootte van een bestaande groep opnieuw beschrijft, geeft u het doelaantal knooppunten op.  De bewerking maken of het formaat wijzigen wordt onmiddellijk voltooid, maar de werkelijke toewijzing van nieuwe knooppunten of het verwijderen van bestaande knooppunten kan enkele minuten duren.  U geeft de time-out voor het wijzigen van het formaat op in de [API voor maken](https://docs.microsoft.com/rest/api/batchservice/pool/add) of het formaat [wijzigen.](https://docs.microsoft.com/rest/api/batchservice/pool/resize) Als Batch het doelaantal knooppunten niet kan verkrijgen tijdens de time-outperiode van het formaat van het formaat, wordt de groep stabiel en worden de groottevan fouten gewijzigd.

De eigenschap [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) voor de meest recente evaluatie geeft de fouten weer die zijn opgetreden.

Veelvoorkomende oorzaken voor het wijzigen van het formaat van fouten zijn:

- Het formaat van een time-out wijzigen is te kort
  - Onder de meeste omstandigheden is de standaardtime-out van 15 minuten lang genoeg om poolknooppunten toe te wijzen of te verwijderen.
  - Als u een groot aantal knooppunten toewijst, raden we u aan de time-out van het formaat te wijzigen op 30 minuten. Bijvoorbeeld wanneer u de grootte van meer dan 1.000 knooppunten van een Azure Marketplace-afbeelding of meer dan 300 knooppunten uit een aangepaste VM-afbeelding herziet.
- Onvoldoende kernquotum
  - Een Batch-account is beperkt in het aantal cores dat het kan toewijzen in alle groepen. Batch stopt met het toewijzen van knooppunten zodra dat quotum is bereikt. U het kernquotum [verhogen](https://docs.microsoft.com/azure/batch/batch-quota-limit) zodat Batch meer knooppunten kan toewijzen.
- Onvoldoende subnet IP's wanneer een [pool zich in een virtueel netwerk bevindt](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Een virtueel netwerksubnet moet voldoende niet-toegewezen IP-adressen hebben om aan elk aangevraagd poolknooppunt toe te wijzen. Anders kunnen de knooppunten niet worden gemaakt.
- Onvoldoende resources wanneer een [pool zich in een virtueel netwerk bevindt](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - U resources maken zoals load-balancers, openbare IP's en netwerkbeveiligingsgroepen in hetzelfde abonnement als het Batch-account. Controleer of de abonnementsquota voldoende zijn voor deze resources.
- Grote groepen met aangepaste VM-afbeeldingen
  - Grote groepen die aangepaste VM-afbeeldingen gebruiken, kunnen langer duren om time-outs toe te wijzen en het formaat ervan te wijzigen.  Zie [Een pool maken met de Galerie Gedeelde afbeeldingen](batch-sig-images.md) voor aanbevelingen over limieten en configuratie.

### <a name="automatic-scaling-failures"></a>Automatische schalingfouten

U Azure Batch ook instellen om het aantal knooppunten in een groep automatisch te schalen. U definieert de parameters voor de [automatische schalingformule voor een groep.](https://docs.microsoft.com/azure/batch/batch-automatic-scaling) De batchservice gebruikt de formule om periodiek het aantal knooppunten in de groep te evalueren en een nieuw doelnummer in te stellen. De volgende soorten problemen kunnen optreden:

- De automatische schaling-evaluatie mislukt.
- De resulterende groottebewerking mislukt en een resultaat.
- Een probleem met de automatische schalingformule leidt tot onjuiste knooppuntdoelwaarden. Het formaat van het formaat werkt of een tijd uit.

U informatie krijgen over de laatste automatische schalingevaluatie met de eigenschap [autoScaleRun.](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) Deze eigenschap rapporteert de evaluatietijd, de waarden en het resultaat en eventuele prestatiefouten.

De [volledige gebeurtenis voor het formaat van de groep](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event) wijzigt informatie over alle evaluaties.

### <a name="delete"></a>Verwijderen

Wanneer u een groep verwijdert die knooppunten bevat, verwijdert Batch eerst de knooppunten. Vervolgens wordt het poolobject zelf verwijderd. Het kan enkele minuten duren voordat de poolknooppunten zijn verwijderd.

Batch stelt de [poolstatus in](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) op verwijderen tijdens **het** verwijderingsproces. De aanroepende toepassing kan detecteren of het verwijderen van de groep te lang duurt met behulp van de **eigenschappen Status** en **StateTransitionTime.**

## <a name="pool-compute-node-errors"></a>Rekenknooppuntfouten bundelen

Zelfs wanneer Batch knooppunten in een groep toewijst, kunnen verschillende problemen ertoe leiden dat sommige knooppunten niet in orde zijn en geen taken kunnen uitvoeren. Deze knooppunten brengen nog steeds kosten met zich mee, dus het is belangrijk om problemen op te sporen om te voorkomen dat ze betalen voor knooppunten die niet kunnen worden gebruikt. Naast veelvoorkomende knooppuntfouten is het handig om de huidige [taakstatus](/rest/api/batchservice/job/get#jobstate) te kennen voor het oplossen van problemen.

### <a name="start-task-failures"></a>Taakfouten starten

U een optionele [starttaak](/rest/api/batchservice/pool/add#starttask) voor een groep opgeven. Zoals bij elke taak u een opdrachtregel en bronbestanden gebruiken om te downloaden vanuit de opslag. De starttaak wordt uitgevoerd voor elk knooppunt nadat deze is gestart. De eigenschap **waitForSuccess** geeft aan of Batch wacht totdat de starttaak is voltooid voordat taken worden gepland op een knooppunt.

Wat gebeurt er als u het knooppunt hebt geconfigureerd om te wachten op het voltooien van een succesvolle starttaak, maar de starttaak mislukt? In dat geval is het knooppunt niet bruikbaar, maar worden er wel kosten in rekening gebracht.

U taakfouten detecteren met behulp van de [eigenschappen resultaat](/rest/api/batchservice/computenode/get#taskexecutionresult) en [failureInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) van de eigenschap [startTaskInfo](/rest/api/batchservice/computenode/get#starttaskinformation) op het hoogste niveau.

Een mislukte starttaak zorgt er ook voor dat Batch de [status](/rest/api/batchservice/computenode/get#computenodestate) van het knooppunt **instelt dat de taak is mislukt** als **waitForSuccess** is ingesteld op **true**.

Zoals met elke taak, kunnen er vele oorzaken voor de start taak niet.  Als u problemen wilt oplossen, controleert u de stdout, stderr en eventuele verdere taakspecifieke logboekbestanden.

Starttaken moeten opnieuw worden uitgevoerd, omdat het mogelijk is dat de starttaak meerdere keren op hetzelfde knooppunt wordt uitgevoerd; de starttaak wordt uitgevoerd wanneer een knooppunt opnieuw wordt weergegeven of opnieuw wordt opgestart. In zeldzame gevallen wordt een starttaak uitgevoerd nadat een gebeurtenis een opnieuw opstarten van het knooppunt heeft veroorzaakt, waarbij een van de besturingssysteem- of kortstondige schijven opnieuw is weergegeven terwijl de andere niet werd weergegeven. Aangezien batchstarttaken (zoals alle batchtaken) vanaf de kortstondige schijf worden uitgevoerd, is dit normaal gesproken geen probleem, maar in sommige gevallen waarin de starttaak een toepassing op de schijf van het besturingssysteem installeert en andere gegevens op de kortstondige schijf bewaart, kan dit leiden tot problemen omdat dingen niet synchroon lopen. Bescherm uw toepassing dienovereenkomstig als u beide schijven gebruikt.

### <a name="application-package-download-failure"></a>Downloadfout van toepassingspakket

U een of meer toepassingspakketten opgeven voor een groep. Batch downloadt de opgegeven pakketbestanden naar elk knooppunt en comprimeert de bestanden nadat het knooppunt is gestart, maar voordat taken zijn gepland. Het is gebruikelijk om een opdrachtregel voor starttaken te gebruiken in combinatie met toepassingspakketten. Bijvoorbeeld om bestanden naar een andere locatie te kopiëren of om de installatie uit te voeren.

De eigenschap [knooppuntfouten](/rest/api/batchservice/computenode/get#computenodeerror) rapporteert een fout die een toepassingspakket niet hebt gedownload en niet-comprimeren. de knooppuntstatus is ingesteld op **onbruikbaar**.

### <a name="container-download-failure"></a>Fout bij het downloaden van containers

U een of meer containerverwijzingen opgeven in een groep. Batch downloadt de opgegeven containers naar elk knooppunt. De eigenschap [knooppuntfouten](/rest/api/batchservice/computenode/get#computenodeerror) rapporteert een fout in het niet downloaden van een container en stelt de knooppuntstatus **in op onbruikbaar**.

### <a name="node-in-unusable-state"></a>Knooppunt in onbruikbare staat

Azure Batch kan de [knooppuntstatus](/rest/api/batchservice/computenode/get#computenodestate) om verschillende redenen **op onbruikbaar** instellen. Met de knooppuntstatus ingesteld op **onbruikbaar,** kunnen taken niet worden gepland op het knooppunt, maar er worden nog steeds kosten in rekening gebracht.

Knooppunten in een **onbruikbare** staat, maar zonder [fouten](/rest/api/batchservice/computenode/get#computenodeerror) betekent dat Batch niet kan communiceren met de VM. In dit geval probeert Batch altijd de VM te herstellen. Batch zal niet automatisch proberen vm's te herstellen die geen toepassingspakketten of containers hebben geïnstalleerd, ook al is de status ervan **onbruikbaar.**

Als Batch de oorzaak kan bepalen, rapporteert de eigenschap [knooppuntfouten](/rest/api/batchservice/computenode/get#computenodeerror) deze.

Andere voorbeelden van oorzaken voor **onbruikbare** knooppunten zijn:

- Een aangepaste VM-afbeelding is ongeldig. Bijvoorbeeld een afbeelding die niet goed is voorbereid.

- Een VM wordt verplaatst vanwege een infrastructuurfout of een upgrade op laag niveau. Batch herstelt het knooppunt.

- Er is een VM-afbeelding geïmplementeerd op hardware die deze niet ondersteunt. Bijvoorbeeld als u een CentOS HPC-afbeelding probeert uit te voeren op een [Standard_D1_v2](../virtual-machines/dv2-dsv2-series.md) VM.

- De VM's bevinden zich in een [virtueel Azure-netwerk](batch-virtual-network.md)en het verkeer is geblokkeerd naar belangrijke poorten.

- De VM's bevinden zich in een virtueel netwerk, maar uitgaand verkeer naar Azure-opslag is geblokkeerd.

- De VM's bevinden zich in een virtueel netwerk met een DNS-configuratie van de klant en de DNS-server kan Azure-opslag niet oplossen.

### <a name="node-agent-log-files"></a>Knooppuntagentlogboekbestanden

Het batchagentproces dat op elk poolknooppunt wordt uitgevoerd, kan logboekbestanden bieden die nuttig kunnen zijn als u contact moet opnemen met ondersteuning over een probleem met een poolknooppunt. Logbestanden voor een knooppunt kunnen worden geüpload via de Azure-portal, Batch Explorer of een [API.](/rest/api/batchservice/computenode/uploadbatchservicelogs) Het is handig om de logbestanden te uploaden en op te slaan. Daarna u het knooppunt of de groep verwijderen om de kosten van de lopende knooppunten op te slaan.

### <a name="node-disk-full"></a>Knooppuntschijf vol

Het tijdelijke station voor een vm met poolknooppunt wordt door Batch gebruikt voor taakbestanden, taakbestanden en gedeelde bestanden.

- Bestanden voor toepassingspakketten
- Taakbronbestanden
- Toepassingsspecifieke bestanden die zijn gedownload naar een van de batchmappen
- Stdout- en stderr-bestanden voor elke uitvoering van taaktoepassingen
- Toepassingsspecifieke uitvoerbestanden

Sommige van deze bestanden worden slechts één keer geschreven wanneer poolknooppunten worden gemaakt, zoals groeptoepassingspakketten of groepstarttaakbronbestanden. Zelfs als slechts een keer geschreven wanneer het knooppunt wordt gemaakt, als deze bestanden te groot zijn, kunnen ze het tijdelijke station vullen.

Andere bestanden worden uitgeschreven voor elke taak die wordt uitgevoerd op een knooppunt, zoals stdout en stderr. Als een groot aantal taken op hetzelfde knooppunt wordt uitgevoerd en/of de taakbestanden te groot zijn, kunnen ze het tijdelijke station vullen.

De grootte van het tijdelijke station is afhankelijk van de VM-grootte. Een overweging bij het kiezen van een VM-grootte is om ervoor te zorgen dat de tijdelijke schijf voldoende ruimte heeft.

- In de Azure-portal kan bij het toevoegen van een groep vm-formaten de volledige lijst met VM-formaten worden weergegeven en is er een kolom 'Resourceschijfgrootte'.
- De artikelen die alle VM-formaten beschrijven, hebben tabellen met een kolom 'Tijdelijke opslag'; bijvoorbeeld [Compute Optimized VM-formaten](/azure/virtual-machines/windows/sizes-compute)

Voor bestanden die door elke taak zijn uitgeschreven, kan voor elke taak een bewaartijd worden opgegeven die bepaalt hoe lang de taakbestanden worden bewaard voordat ze automatisch worden opgeschoond. De bewaartijd kan worden verkort om de opslagvereisten te verlagen.


Als de tijdelijke schijf geen ruimte meer heeft (of bijna geen ruimte meer heeft), wordt het knooppunt verplaatst naar [de onbruikbare](/rest/api/batchservice/computenode/get#computenodestate) status en wordt een knooppuntfout gerapporteerd waarin staat dat de schijf vol is.

### <a name="what-to-do-when-a-disk-is-full"></a>Wat te doen als een schijf vol is

Bepaal waarom de schijf vol is: als u niet zeker weet wat er ruimte in beslag neemt op het knooppunt, is het raadzaam om de afstandsbediening naar het knooppunt af te zetten en handmatig te onderzoeken waar de ruimte is gebleven. U ook gebruik maken van de [API batchlijstbestanden](https://docs.microsoft.com/rest/api/batchservice/file/listfromcomputenode) om bestanden in batchbeheermappen te onderzoeken (bijvoorbeeld taakuitvoer). Houd er rekening mee dat deze API alleen bestanden in de batchbeheermappen bevat en dat uw taken elders bestanden hebben gemaakt, u ze niet ziet.

Zorg ervoor dat alle gegevens die u nodig hebt, zijn opgehaald uit het knooppunt of zijn geüpload naar een duurzame winkel. Alle beperking van de schijf-volledige probleem te betrekken verwijderen van gegevens vrij te maken ruimte.

### <a name="recovering-the-node"></a>Het knooppunt herstellen

1. Als uw pool een [C.loudServiceConfiguration-groep](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) is, u het knooppunt opnieuw weer weer in beeld brengen via de [API voor batchre-image.](https://docs.microsoft.com/rest/api/batchservice/computenode/reimage) Dit zal de hele schijf schoon te maken. Re-image wordt momenteel niet ondersteund voor [VirtualMachineConfiguration-groepen.](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)

2. Als uw pool een [VirtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)is, u het knooppunt uit de groep verwijderen met behulp van de [API voor verwijderen knooppunten.](https://docs.microsoft.com/rest/api/batchservice/pool/removenodes) Vervolgens u het zwembad opnieuw laten groeien om het slechte knooppunt te vervangen door een nieuwe.

3.  Oude voltooide taken of oude voltooide taken verwijderen waarvan de taakgegevens zich nog steeds op de knooppunten begeven. Voor een hint naar wat taken/takengegevens op de knooppunten staan, u kijken in de [verzameling RecentTasks](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskinformation) op het knooppunt of naar de [bestanden op het knooppunt.](https://docs.microsoft.com//rest/api/batchservice/file/listfromcomputenode) Als u de taak verwijdert, worden alle taken in de taak verwijderd en wordt er ruimte vrijgemaakt voor het verwijderen van de taken in de taakmappen op het knooppunt, waardoor er ruimte vrijkomt. Zodra u genoeg ruimte hebt vrijgemaakt, start u het knooppunt opnieuw op en moet het de status "Onbruikbaar" en opnieuw in "Idle" worden verplaatst.

## <a name="next-steps"></a>Volgende stappen

Controleer of u uw toepassing hebt ingesteld om uitgebreide foutcontrole te implementeren, vooral voor asynchrone bewerkingen. Het kan van cruciaal belang zijn om problemen onmiddellijk op te sporen en te diagnosticeren.
