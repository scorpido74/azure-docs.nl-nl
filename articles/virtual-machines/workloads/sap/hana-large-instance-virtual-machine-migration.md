---
title: Migreren van SAP HANA op Azure (grote exemplaren) naar Azure virtual machines | Microsoft Docs
description: SAP HANA op Azure (grote exemplaren) migreren naar Azure virtual machines
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2dd01bf60104939fcf1f9bd1ccec0e7d72710041
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154918"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA voor migratie van grote Azure-instanties naar Azure Virtual Machines
In dit artikel worden mogelijke scenario's voor grootschalige implementaties in azure beschreven en wordt de aanpak voor planning en migratie met een geminimaliseerde overgangs downtime geboden

## <a name="overview"></a>Overzicht
Sinds de aankondiging van de Azure large instances voor SAP HANA (HLI) in september 2016, hebben veel klanten deze hardware als een service toegepast voor hun in-Memory Compute platform.  In de afgelopen jaren is de schaal baarheid van de Azure VM-extensie gecombineerd met de ondersteuning van HANA scale-out-implementatie model de meeste vraag naar ERP-data base-capaciteit voor ondernemingen overschreden.  We beginnen klanten de wens te zien om hun SAP HANA werk belasting van fysieke servers naar virtuele Azure-machines te migreren.
Deze hand leiding is geen stap-voor-stap configuratie document, maar hierin worden de algemene implementatie modellen en de voor bereiding van de aanbiedingen beschreven, met het voor nemen van de nodige overwegingen om de overgangs tijd te minimaliseren.

## <a name="assumptions"></a>Veronderstellingen
In dit artikel worden de volgende veronderstellingen:
- De enige beoordeelde rente is een homogene HANA data base Compute Service-migratie van Hana large instance (HLI) naar Azure VM zonder aanzienlijke software-upgrade of patching. Deze kleine updates omvatten het gebruik van een recentere versie van het besturings systeem of HANA-versie expliciet vermeld zoals ondersteund door de relevante SAP-opmerkingen. 
- Alle updates/upgrade activiteiten, indien gewenst, moeten worden uitgevoerd vóór of na de migratie.  Bijvoorbeeld SAP HANA MCOS conversie naar MDC-implementatie. 
- De migratie benadering die de minste downtime zou bieden, is SAP HANA systeem replicatie. Andere migratie methoden maken geen deel uit van het bereik van dit document.
- Dit is van toepassing voor zowel Rev3-als Rev4-Sku's van HLI.
- De HANA-implementatie architectuur blijft in de eerste plaats ongewijzigd tijdens de migratie.  Dat wil zeggen dat een systeem met één exemplaar van DR op dezelfde manier blijft op het doel.
- Klanten hebben de Service Level Agreement (SLA) van de doel architectuur (to-to-to-to-to-to-to-to-to-out) 
- Vanwege de verschillen in commerciële termen tussen HLIs en Vm's, moeten klanten het gebruik van hun Vm's controleren voor kosten beheer.
- Klanten begrijpen en erkennen dat HLI een specifiek reken platform is. Vm's worden echter uitgevoerd op een gedeelde infra structuur, die is beveiligd en geïsoleerd van andere tenants.
- Klanten hebben gevalideerd dat doel-Vm's uw beoogde architectuur ondersteunen. Controleer de [SAP Hana hardware-map](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)voor een overzicht van alle ondersteunde VM-sku's die zijn gecertificeerd voor SAP Hana-implementatie.
- Klanten hebben het ontwerp en migratie plan gevalideerd.
- Plan het knoop punt voor nood herstel samen met de primaire site.  Klanten kunnen na de migratie het HLI DR-knoop punt niet gebruiken voor de primaire site die op Vm's wordt uitgevoerd.
- Klanten hebben de vereiste back-upbestanden gekopieerd naar doel-Vm's, op basis van de vereisten voor bedrijfs herstel en naleving. Dit is de nood zaak voor herstel naar een bepaald tijdstip tijdens de overgangs periode.
- Voor HSR HA moeten klanten het STONITH-apparaat instellen en configureren per SAP HANA HA-hand leidingen voor [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) en [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker).  Het is niet vooraf geconfigureerd zoals de HLI-case.
- Deze migratie aanpak heeft geen betrekking op de HLI Sku's met Optane-configuratie.

## <a name="deployment-scenarios"></a>Implementatiescenario's
De algemene implementatie modellen met HLI-klanten worden in de volgende tabel samenvatten.  Migratie naar Azure Vm's voor alle HLI-scenario's is mogelijk.  Voor een aantal scenario's is mogelijk een kleine architectuur wijziging nodig om de voor delen van de huidige Azure service-aanbiedingen te verkrijgen.

| Scenario-ID | HLI-scenario | Migreren naar VM-Verbatim? | Opmerkingen |
| --- | --- | --- | --- |
| 1 | [Eén knoop punt met één SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-one-sid) | Ja | - |
| 2 | [Eén knoop punt met MCOS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) | Ja | - |
| 3 | [Eén knoop punt met behulp van opslag replicatie met DR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication) | Nee | Opslag replicatie is niet beschikbaar met Azure Virtual platform, wijzig de huidige oplossing voor nood herstel naar HSR of Backup/Restore |
| 4 | [Eén knoop punt met DR (Multipurpose) met behulp van storage-replicatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication) | Nee | Opslag replicatie is niet beschikbaar met Azure Virtual platform, wijzig de huidige oplossing voor nood herstel naar HSR of Backup/Restore |
| 5 | [HSR met STONITH voor hoge Beschik baarheid](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability) | Ja | Geen vooraf geconfigureerde SBD voor doel-Vm's.  Selecteer en implementeer een STONITH-oplossing.  Mogelijke opties: Azure omheinings agent (ondersteund voor zowel [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker), [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)), SBD |
| 6 | [HA met HSR, DR met opslag replicatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication) | Nee | Opslag replicatie voor nood gevallen vervangen door HSR of Backup/Restore |
| 7 | [Automatische failover van host (1 + 1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11) | Ja | ANF gebruiken voor gedeelde opslag met Azure-Vm's |
| 8 | [Uitschalen met stand-by](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby) | Ja | BW/4HANA met M128s, M416s, M416ms Vm's met behulp van ANF alleen voor opslag |
| 9 | [Uitschalen zonder stand-by](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby) | Ja | BW/4HANA met M128s-, M416s-, M416ms-Vm's (met of zonder gebruik van ANF voor opslag) |
| 10 | [Uitschalen met behulp van de opslag replicatie met DR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication) | Nee | Opslag replicatie voor nood gevallen vervangen door HSR of Backup/Restore |
| 11 | [Eén knoop punt met DR met behulp van HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr) | Ja | - |
| 12 | [HSR van één knoop punt naar DR (kosten geoptimaliseerd)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized) | Ja | - |
| 13 | [HA en DR met HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr) | Ja | - |
| 14 | [HA en DR met HSR (kosten geoptimaliseerd)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Ja | - |
| 15 | [Uitschalen met DR met behulp van HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr) | Ja | BW/4HANA met M128s. M416s, M416ms Vm's (met of zonder gebruik van ANF voor opslag) |


## <a name="source-hli-planning"></a>Bron (HLI) planning
Bij het uitvoeren van een HLI-server hebben zowel het Service beheer van micro soft als klanten de planning van de berekenings-, netwerk-, opslag-en OS-specifieke instellingen door lopen om de SAP HANA-data base uit te voeren.  Vergelijk bare planningen moeten worden uitgevoerd voor de migratie naar Azure VM.

### <a name="sap-hana-housekeeping"></a>SAP HANA housekeeping 
Voordat u de HANA-data base migreert, is het een goed idee om de inhoud van de Data Base op te ruimen, zodat ongewenste, verouderde gegevens of verlopen logboeken niet worden gemigreerd naar de nieuwe data base.  Housekeeping omvat over het algemeen het verwijderen of archiveren van oude, verlopen of inactieve gegevens.  Daarom moeten deze acties voor gegevens hygiëne worden getest in niet-productie systemen om de geldigheid van de gegevens te valideren voordat het productie gebruik wordt uitgevoerd.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Netwerk verbinding toestaan voor nieuwe Vm's en, of virtueel netwerk 
In de HLI-implementatie van een klant is de netwerk verbinding van Azure vnets tot stand gebracht op basis van de informatie die is beschreven in het artikel [SAP Hana (grote exemplaren) netwerk architectuur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Ook wordt netwerk verkeer routering uitgevoerd op de wijze die wordt beschreven in de sectie ' route ring in azure '.
1. Voor het instellen van een nieuwe virtuele machine als het HSR-doel voor de migratie, als de nieuwe virtuele machine van Azure wordt geplaatst in een bestaand virtueel netwerk met IP-adresbereiken, die al gemachtigd zijn om verbinding te maken met het HLI-knoop punt, is er geen verdere update voor de HLI vereist
2. Als de nieuwe virtuele machine van Azure wordt geplaatst in een nieuw virtueel netwerk (zich mogelijk in een andere regio bevindt) en moet worden gekoppeld aan het bestaande virtuele netwerk, kunnen de ExpressRoute-service sleutel en de resource-ID van de oorspronkelijke HLI-inrichting worden gebruikt om toegang te verlenen tot dit nieuwe IP-adres bereik van het virtuele netwerk.  Coördineer met het micro soft-Service beheer om het virtuele netwerk naar de HLI-connectiviteit in te scha kelen.  Opmerking: om de netwerk latentie tussen de toepassing en de database lagen te minimaliseren, moeten zowel de toepassing als de database lagen zich in hetzelfde virtuele netwerk bezien.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group"></a>Bestaande app Layer Availability set, Beschikbaarheidszones en proximity placement Group
Het huidige implementatie model wordt uitgevoerd om te voldoen aan bepaalde serviceniveau doelstellingen.  Zorg er bij deze overstap voor dat de doel infrastructuur voldoet aan de ingestelde doel stellingen of deze overschrijdt.  
Meer waarschijnlijk dan niet, klanten SAP-toepassings servers worden in een beschikbaarheidsset geplaatst.  Als het huidige implementatie service niveau voldoende is en 
- Als de vervanging van de SAP HANA-server via naam omzetting geschiedt door de IP-adressen voor de data base-hostnaam uit te wisselen, hoeft u verder niets te doen.  
- Als u geen gebruik maakt van PPG, moet u ervoor zorgen dat u alle toepassings-en database servers in dezelfde zone plaatst om de netwerk latentie te minimaliseren.
- Als u PPG gebruikt, raadpleegt u de sectie van deze document doel planning, Beschikbaarheidsset, Beschikbaarheidszones en proximity placement Group (PPG)

### <a name="storage-replication-discontinuance-process-if-used"></a>Proces voor het door gaan van de opslag replicatie (indien gebruikt)
Als opslag replicatie wordt gebruikt als de nood herstel oplossing, moet de replicatie worden beëindigd (niet gepland) nadat de SAP-toepassing is afgesloten en de laatste SAP HANA catalogus, het logboek bestand, gegevens back-ups zijn gerepliceerd naar de externe opslag volumes.  Deze actie is een conservatoire tactiek dat we een actuele data base hebben op de DR-HLI als er sprake is van een nood geval tijdens de fysieke naar Azure VM-overgang.

### <a name="data-backups-preservation-consideration"></a>Overweging bij het behoud van gegevens back-ups
Nadat de cutover is SAP HANA op Azure VM, zijn alle op moment opnamen gebaseerde gegevens of logboek back-ups op de HLI niet eenvoudig toegankelijk of kunnen ze zo nodig naar een virtuele machine worden gemigreerd. Voor de vroege overgangs periode is het raadzaam om, voordat de back-up van Azure voldoende geschiedenis heeft om te voldoen aan de vereisten voor herstel naar een bepaald tijdstip, een back-up op bestands niveau te maken naast moment opnamen op de HLI, dagen/weken vóór cutover.  Deze back-ups worden gekopieerd naar een Azure Storage-account dat toegankelijk is voor de nieuwe SAP HANA VM. Naast het maken van een back-up van de HLI, is het ook een prudente manier om de volledige back-ups van het SAP-landschap snel toegankelijk te maken voor het geval een terugdraai actie nodig is.

### <a name="adjusting-system-monitoring"></a>Systeem bewaking aanpassen 
Klanten gebruiken veel verschillende hulpprogram ma's voor het bewaken en verzenden van waarschuwings meldingen voor systemen binnen hun SAP-landschap.  Dit item is slechts een eenvoudige aanroep voor een geschikte actie bij het toevoegen van de nieuwe virtuele machine (s) die eventuele aanpassingen voor het controleren en bijwerken van de ontvangers van waarschuwings meldingen, indien nodig.

### <a name="microsoft-operations-team-involvement"></a>Betrokkenheid van micro soft Operations-team 
Open een ticket van de Azure Portal bases op de bestaande instantie van HLI.  Zodra het ondersteunings ticket is gemaakt, neemt een ondersteunings technicus via e-mail contact met u op.  

### <a name="engage-microsoft-account-team"></a>Micro soft-account team benaderen
Plan migratie bijna op het tijdstip van de vernieuwings tijd van het HLI-contract om onnodige kosten voor de reken resource te minimaliseren. Voor het buiten gebruik stellen van de HLI Blade is het vereist om de contract beëindiging en de werkelijke afsluiting van de eenheid te coördineren.

## <a name="destination-planning"></a>Doel planning
Stel een nieuwe infra structuur in om ervoor te zorgen dat de nieuwe toevoeging in het grote schema van dingen zal worden uitgevoerd.  Hieronder vindt u enkele belang rijke punten voor contemplation.

### <a name="resource-availability-in-the-target-region"></a>Beschik baarheid van resources in de doel regio 
De huidige implementatie regio van de SAP-toepassings server bevindt zich doorgaans in de buurt van de bijbehorende HLIs.  HLIs worden echter aangeboden op minder locaties dan de beschik bare Azure-regio's.  Bij de migratie van de fysieke HLI naar Azure VM biedt de kans op het verfijnen van de afstand van alle gerelateerde services voor prestatie optimalisatie.  Als u dit doet, is het belang rijk om ervoor te zorgen dat de gekozen regio de gewenste bronnen heeft.  Bijvoorbeeld de beschik baarheid van bepaalde VM-familie of het aanbieden van Azure-zones voor hoge Beschik baarheid.

### <a name="virtual-network"></a>Virtueel netwerk 
De infrastructuur architect wordt gewend met een keuze om te bepalen of u de nieuwe HANA-data base in het bestaande virtuele netwerk van de SAP-toepassing wilt uitvoeren of een nieuwe wilt maken.  De primaire beslissende factor is de huidige netwerk indeling voor het SAP-landschap.  In het geval van een wijziging van de implementatie-infra structuur wordt rekening gehouden met bijvoorbeeld een implementatie van één zone naar een twee zones en PPG te gebruiken. Deze Beschik baarheid brengt architectuur wijzigingen met zich mee. Zie het artikel [Azure PPG voor optimale netwerk latentie met SAP-toepassing](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)voor meer informatie.   

### <a name="security"></a>Beveiliging
Of de nieuwe SAP HANA VM in een nieuw of bestaand vnet/subnet staat, een nieuwe bedrijfskritische service die moet worden beveiligd.  Het juiste toegangs beheer dat compatibel is met het beveiligings beleid van de bedrijfs informatie voor deze nieuwe service klasse moet worden geëvalueerd en geïmplementeerd. 

### <a name="vm-sizing-recommendation"></a>Aanbeveling voor VM-grootte
Deze migratie is ook een kans om de grootte van uw HANA Compute-engine naar rechts te wijzigen.  Eén kan gebruikmaken van HANA- [systeem weergaven](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) in combi natie met Hana Studio om inzicht te krijgen in het verbruik van systeem bronnen, waardoor het mogelijk is om de efficiëntie van bestedingen te verlagen.

### <a name="storage"></a>Opslag 
Opslag prestaties zijn een van de factoren die van invloed zijn op de gebruikers ervaring van de SAP-toepassing.  Op basis van een bepaalde VM-SKU zijn er minimale aanbevelingen voor opslag indeling gepubliceerd [SAP Hana opslag configuraties voor virtuele Azure-machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). We raden u aan deze minimale specificaties te controleren en te vergelijken met de bestaande HLI-systeem statistieken om te zorgen voor voldoende i/o-capaciteit en prestaties voor de nieuwe HANA-VM.

Als u PPG voor de nieuwe HANA-VM en de bijbehorende servers configureert, dient u een ondersteunings ticket in om de plaatsing van de opslag en de HANA-VM te controleren en te controleren.  
Omdat uw back-upoplossing mogelijk moet worden gewijzigd, moeten de opslag kosten ook worden gestart om te voor komen dat operationele uitgaven aflopen. 

### <a name="storage-replication-for-disaster-recovery"></a>Opslag replicatie voor herstel na nood gevallen
In HLI werd opslag replicatie aangeboden als de standaard optie voor de nood herstel replicatie voor de HANA-data base. Deze functie is niet de standaard optie voor Azure VM. Overweeg HSR, back-ups maken/herstellen of andere ondersteunde oplossingen die voldoen aan de behoeften van uw bedrijf.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Beschikbaarheids sets, Beschikbaarheidszones en proximity-plaatsings groepen 
Bij het minimaliseren van de afstand tussen de toepassingslaag en SAP HANA om de netwerk latentie ten minste te houden, moeten de nieuwe data base-VM en de huidige SAP-toepassings servers worden geplaatst in een proximity-plaatsings groep (PPG). Raadpleeg [proximity placementing Group](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) voor meer informatie over hoe Azure-beschikbaarheidsset en Beschikbaarheidszones werken met PPG voor SAP-implementaties.
Als leden van het doel-HANA-systeem in meer dan één Azure-zone zijn geïmplementeerd, moeten klanten een duidelijke weer gave hebben van het latentie Profiel van de gekozen zones. De plaatsing van SAP-systeem onderdelen is optimaal met betrekking tot de Proximal-afstand tussen de SAP-toepassing en de data base.  Met het [hulp programma latentie test](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) van de open bare Domein beschikbaarheids zone kunt u de meting eenvoudiger maken.  


### <a name="backup-strategy"></a>Back-upstrategie
Veel klanten maken al gebruik van back-upoplossing van derden voor SAP HANA op HLI.  In dat geval moeten alleen een extra beveiligde VM en HANA-data bases worden geconfigureerd.  Doorlopende HLI-back-uptaken voor nu worden niet meer gepland als de machine na de migratie buiten gebruik wordt gesteld.
Azure backup voor SAP HANA op VM is nu algemeen beschikbaar.  Zie de volgende koppelingen voor gedetailleerde informatie over: [back-ups maken](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database), [herstellen](https://docs.microsoft.com/azure/backup/sap-hana-db-restore), [beheren](https://docs.microsoft.com/azure/backup/sap-hana-db-manage) SAP Hana back-up in virtuele machines van Azure.

### <a name="dr-strategy"></a>DR-strategie
Als uw serviceniveau doelstellingen een langere herstel tijd van een eenvoudige back-up naar Blob-opslag en herstel op locatie of naar een nieuwe virtuele machine bieden, is het de eenvoudigste en minst dure DR-strategie.  
Net als het platform voor grote instanties waarbij HANA DR normaal gesp roken wordt uitgevoerd met HSR; In azure VM is HSR ook de meest natuurlijke en systeem eigen SAP HANA DR-oplossing.  Ongeacht of de bron implementatie één exemplaar is, geclusterd met of zonder automatische failover of meerdere knoop punten scale-out HANA, is een doel-HSR replica van de bron infrastructuur vereist in de DR-regio. Deze HSR-doel-DR-replica wordt tot stand gebracht nadat de primaire HLI naar de VM-migratie is voltooid.  De DR HANA-instantie wordt geregistreerd bij de primaire SAP HANA op het VM-exemplaar als een secundaire replicatie site.  

### <a name="sap-application-server-connectivity-destination-change"></a>Wijziging van connectiviteits doel voor SAP-toepassings server
De HSR-migratie aanpak resulteert in een nieuwe HANA DB-host en daarom een nieuwe data base-hostnaam voor de toepassingslaag, moeten SAP-profielen worden aangepast aan de nieuwe hostnaam.  Als de switch wordt uitgevoerd door de naam omzetting die de hostnaam behoudt, is er geen profiel wijziging vereist.

### <a name="operating-system"></a>Besturingssysteem
De installatie kopieën van het besturings systeem voor HLI en VM, zelfs als ze zich op hetzelfde release niveau bevinden, is SLES 11 SP4 bijvoorbeeld niet identiek. Klanten moeten de vereiste pakketten, hotfixes, patches, kernel en beveiligings oplossingen op de HLI valideren zodat deze kunnen worden geïnstalleerd op het besturings systeem van de doel-VM.  Verder is het gebruikelijk dat klanten een recentere versie van het besturings systeem op de doel-VM voor SAP HSR configureren.  Dit wordt ondersteund per [SAP-opmerking 2763388](https://launchpad.support.sap.com/#/notes/2763388).

### <a name="new-sap-license-request"></a>Nieuwe SAP-licentie aanvraag
Een eenvoudige aanroep om een nieuwe SAP-licentie voor het nieuwe HANA-systeem aan te vragen, nu dat is gemigreerd naar Vm's.

### <a name="service-level-agreement-sla-differences"></a>Verschillen in de Service Level Agreement (SLA)
De auteurs zouden het verschil van de beschik baarheid van de SLA tussen HLI en Azure VM kunnen aanroepen.  Zo bieden geclusterde HLIs HA-paren een Beschik baarheid van 99,99%. Als u dezelfde SLA wilt uitvoeren, moet een virtuele machine in beschikbaarheids zones worden geïmplementeerd. In dit [artikel](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) wordt de beschik baarheid van gekoppelde implementatie architecturen beschreven, zodat klanten de doel infrastructuur dienovereenkomstig kunnen plannen.


## <a name="migration-strategy"></a>Migratiestrategie
In dit document hebben we alleen de HANA-systeem replicatie benadering voor de migratie van HLI naar Azure VM.  Afhankelijk van de doel opslag oplossing die is geïmplementeerd, verschilt het proces enigszins. De stappen op hoog niveau worden hieronder beschreven.

### <a name="vm-with-premiumultra-disks-for-data"></a>VM met Premium/Ultra-disks voor gegevens
Voor virtuele machines die zijn geïmplementeerd met Premium of Ultra disk, wordt de standaard configuratie van SAP HANA systeem replicatie toegepast en kan deze worden gebruikt voor het instellen van HSR.  In het [SAP Help-artikel](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) waarnaar wordt verwezen, vindt u een overzicht van de stappen voor het instellen van de systeem replicatie tussen twee systemen, het uitvoeren van een secundair systeem, het afhandelen van een primair systeem en het uitschakelen van systeem replicatie.  Voor het doel van de migratie zijn alleen de installatie, het gebruik en het uitschakelen van systeem replicatie op de HLI-bron stappen vereist.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>VM met ANF voor gegevens-en logboek volumes
Op hoog niveau moeten de nieuwste HLI-opslag momentopnamen van de volledige gegevens en logboek volumes worden gekopieerd naar Azure Storage waar ze toegankelijk zijn en kunnen worden hersteld door de doel-HANA-VM.  Het kopieer proces kan worden uitgevoerd met alle systeem eigen Linux-Kopieer hulpprogramma's.  

>[!Important]
> Kopiëren en gegevens overdracht kan uren duren, afhankelijk van de grootte van de HANA-data base en de netwerk bandbreedte.  Het meren deel van het kopieer proces moet vóór de uitval tijd van de primaire HANA DB worden uitgevoerd.

### <a name="mcos-to-mdc-conversion"></a>Conversie van MCOS naar MDC
Het implementatie model voor meerdere onderdelen in één systeem (MCOS) is door een aantal van onze HLI-klanten gekozen.  De motivatie was het omzeilen van de beperking van de MDC-opslag momentopname (Multiple data bases container) van eerdere SAP HANA versies.  In het MCOS-model worden meerdere onafhankelijke instanties van de SAP HANA gestapeld op één HLI-Blade.  Het gebruik van HSR voor de migratie vertoont het resultaat in meerdere HANA Vm's met één Tenant database.  Met deze eind status wordt een busier-landschap gemaakt dat mogelijk is voor een klant met accustom.  Met SAP HANA 2,0 standaard implementatie die wordt MDC, is een levensvatbaar alternatief voor het uitvoeren van [Hana-Tenant verplaatsing](https://launchpad.support.sap.com/#/notes/2472478) na de HSR-migratie.  Met dit proces worden deze onafhankelijke HANA-data bases samengevoegd in cotenants in één enkele HANA-container.  

### <a name="application-layer-consideration"></a>Overweging voor de toepassingslaag
De DB-server wordt weer gegeven als Center van een SAP-systeem.  Alle toepassings servers moeten zich in de buurt van de SAP HANA DB bevinden.  In sommige gevallen, wanneer een nieuw gebruik van PPG gewenst is, wordt het verplaatsen van bestaande toepassings servers naar de PPG waar de HANA-VM mogelijk is vereist.  Het maken van nieuwe toepassings servers kan eenvoudiger worden aangemerkt als u al een implementatie sjabloon hebt.  
Als bestaande toepassings servers en de nieuwe HANA-VM optimaal zijn gevonden, moeten er geen nieuwe toepassings servers worden gebouwd, tenzij extra capaciteit gewenst is.  
Als er een nieuwe infra structuur is gebouwd om de beschik baarheid van de service te verbeteren, kunnen de bestaande toepassings servers onnodig worden en moet deze worden afgesloten en worden verwijderd.
Als de hostnaam van de doel-VM is gewijzigd en verschilt van de HLI-hostnaam, moeten SAP-toepassings server profielen worden aangepast zodat deze naar de nieuwe host wijzen.  Als alleen het IP-adres van de HANA DB is gewijzigd, is er een update van de DNS-record nodig om binnenkomende verbindingen met de nieuwe HANA-VM aan te leiden.

### <a name="acceptance-test"></a>Acceptatie test
Hoewel de migratie van HLI naar VM geen aanmerkelijke wijzigingen in de inhoud van de Data Base verzorgt in vergelijking met een heterogene migratie, raden we u aan om de belangrijkste functies en het prestatie aspect van de nieuwe instellingen te valideren.  

### <a name="cutover-plan"></a>Cutover-plan
Hoewel deze migratie direct vooruit gaat, is het echter wel het buiten gebruik stellen van een bestaande data base.  Zorgvuldige planning om het bron systeem te bewaren met de bijbehorende inhoud en back-upkopieën zijn kritiek als terugval nood zakelijk is.  Een goede planning biedt een spoedigere terugboeking.   


## <a name="post-migration"></a>Na de migratie
De migratie taak wordt pas uitgevoerd als er een door de HLI afhankelijke services of verbindingen zijn losgekoppeld om ervoor te zorgen dat de gegevens integriteit behouden blijft.  Sluit ook overbodige services af.  In deze sectie vindt u enkele belang rijke items.

### <a name="decommissioning-the-hli"></a>De HLI buiten gebruik stellen
Na een geslaagde migratie van de HANA-Data Base naar Azure VM, moeten er geen bedrijfs transacties voor productie worden uitgevoerd op de HLI-data base.  Het is echter wel mogelijk dat de HLI actief blijft gedurende een periode die gelijk is aan het lokale Bewaar venster voor back-upgegevens.  Alleen dan moet de HLI worden buiten gebruik gesteld.  Naast de technische verbintenis, moeten klanten in hun beste belangen de HLI-toezeg gingen met micro soft contractueel sluiten.  Klanten kunnen contact opnemen met hun micro soft-vertegenwoordigers om via het uit te zetten proces van de HLI te werken.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Verwijder alle proxy's (bijvoorbeeld iptables, BIGIP) die zijn geconfigureerd voor HLI 
Als een proxy service, zoals die van een IPTables wordt gebruikt om on-premises verkeer naar en van de HLI te routeren, is die service niet meer nodig na de geslaagde migratie naar de VM.  Deze connectiviteits service moet echter worden bewaard zolang de HLI-Blade nog steeds wordt verzorgd, zoals wordt beschreven in het onderwerp uit bedrijf nemen.  Deze service kan worden afgesloten nadat de Blade HLI volledig uit bedrijf is genomen. 

### <a name="remove-global-reach-for-hli"></a>Global Reach voor HLI verwijderen 
Global Reach wordt doorgaans gebruikt om verbinding te maken met de ExpressRoute-gateway van de klant met de HLI ExpressRoute-gateway, zodat het on-premise verkeer van de klant direct kan worden bereikt zonder dat er een proxy service nodig is.  Net als bij de service IPTables proxy moet GlobalReach worden bewaard totdat de HLI volledig uit bedrijf is genomen.

### <a name="operating-system-subscription--movereuse"></a>Abonnement van het besturings systeem: verplaatsen/hergebruik
Wanneer de VM-servers zijn opgelopen en de HLI-Blades buiten gebruik worden gesteld, kunnen de besturingssysteem abonnementen worden vervangen of opnieuw worden gebruikt om te voor komen dat de OS-licenties dubbel worden betaald.



## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:
- [SAP Hana infrastructuur configuraties en-bewerkingen op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
- [SAP-workloads op Azure: controle lijst voor planning en implementatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist).
