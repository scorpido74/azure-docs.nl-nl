---
title: SAP HANA migreren op Azure (Large Instances) naar virtuele Azure-machines| Microsoft Documenten
description: SAP HANA migreren op Azure (Large Instances) naar virtuele Azure-machines
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fd1267711871b3e55f1a6229e46ae27b360322f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617045"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA op Azure Large Instance-migratie naar Azure Virtual Machines
In dit artikel worden mogelijke Azure Large Instance-implementatiescenario's beschreven en wordt de planning en migratiebenadering met minimale overgangsdowntime

## <a name="overview"></a>Overzicht
Sinds de aankondiging van de Azure Large Instances for SAP HANA (HLI) in september 2016 hebben veel klanten deze hardware overgenomen als een serviceaanbod voor hun in-memory compute-platform.  In de afgelopen jaren heeft de Azure VM-extensie in combinatie met de ondersteuning van hana-scale-out-implementatie de vraag naar ERP-databasecapaciteit van de meeste zakelijke klanten overschreden.  We zien dat klanten de interesse hebben om hun SAP HANA-workload te migreren van fysieke servers naar Azure VM's.
Deze handleiding is geen stapsgewijs configuratiedocument. Het beschrijft de gemeenschappelijke implementatiemodellen en biedt plannings- en migratieadviezen.  De bedoeling is om de nodige overwegingen voor de voorbereiding te roepen om de downtime van de overgang te minimaliseren.

## <a name="assumptions"></a>Veronderstellingen
In dit artikel worden de volgende aannames aangenomen:
- De enige rente die wordt overwogen is een homogene HANA-database compute service-migratie van Hana Large Instance (HLI) naar Azure VM zonder significante software-upgrade of patching. Deze kleine updates omvatten het gebruik van een meer recente OS-versie of HANA-versie expliciet vermeld als ondersteund door relevante SAP-notities. 
- Alle updates/upgrades activiteiten moeten worden gedaan voor of na de migratie.  Sap HANA MCOS bijvoorbeeld converteren naar MDC-implementatie. 
- De migratiebenadering die de minste downtime zou bieden, is SAP HANA-systeemreplicatie. Andere migratiemethoden maken geen deel uit van het bereik van dit document.
- Deze richtlijnen zijn van toepassing op zowel Rev3- als Rev4 SKU's van HLI.
- Hana-implementatiearchitectuur blijft voornamelijk ongewijzigd tijdens de migratie.  Dat wil zeggen, een systeem met single instance DR blijft op dezelfde manier op de bestemming.
- Klanten hebben de SERVICE Level Agreement (SLA) van de doelarchitectuur (to-be) beoordeeld en begrepen. 
- De commerciële voorwaarden tussen HLI's en VM's zijn verschillend. Klanten moeten het gebruik van hun VM's controleren voor kostenbeheer.
- Klanten begrijpen dat HLI een speciaal compute platform is, terwijl VM's draaien op gedeelde maar geïsoleerde infrastructuur.
- Klanten hebben gevalideerd dat doel VM's uw beoogde architectuur ondersteunen. Zie de [SAP HANA-hardwaremap](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)om alle ondersteunde VM SKU's te bekijken die zijn gecertificeerd voor SAP HANA-implementatie.
- Klanten hebben het ontwerp- en migratieplan gevalideerd.
- Plan voor crash recovery VM samen met de primaire site.  Klanten kunnen de HLI niet gebruiken als het DR-knooppunt voor de primaire site die na de migratie op VM's wordt uitgevoerd.
- Klanten kopieerden de vereiste back-upbestanden naar VM's, op basis van vereisten voor herstelbaarheid en naleving van bedrijven. Met VM-toegankelijke back-ups maakt het point-in-time herstel tijdens de overgangsperiode mogelijk.
- Voor HSR HA moeten klanten het STONITH-apparaat per SAP HANA HA-handleidingen voor [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) en [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)instellen en configureren.  Het is niet vooraf geconfigureerd zoals de HLI zaak.
- Deze migratiebenadering heeft geen betrekking op de HLI SKU's met Optane-configuratie.

## <a name="deployment-scenarios"></a>Implementatiescenario's
Algemene implementatiemodellen met HLI-klanten worden samengevat in de volgende tabel.  Migratie naar Azure VM's voor alle HLI-scenario's is mogelijk.  Om te profiteren van aanvullende Azure-services die beschikbaar zijn, zijn mogelijk kleine architectonische wijzigingen vereist.

| Scenario-id | HLI-scenario | Migreren naar VM letterlijk? | Opmerking |
| --- | --- | --- | --- |
| 1 | [Eén knooppunt met één SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-one-sid) | Ja | - |
| 2 | [Eén knooppunt met MCOS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) | Ja | - |
| 3 | [Eén knooppunt met DR met opslagreplicatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication) | Nee | Opslagreplicatie is niet beschikbaar met het virtuele Azure-platform, wijzigt de huidige DR-oplossing in HSR of back-up/herstel |
| 4 | [Eén knooppunt met DR (multifunctioneel) met opslagreplicatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication) | Nee | Opslagreplicatie is niet beschikbaar met het virtuele Azure-platform, wijzigt de huidige DR-oplossing in HSR of back-up/herstel |
| 5 | [HSR met STONITH voor hoge beschikbaarheid](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability) | Ja | Geen vooraf geconfigureerde SBD voor doel-VM's.  Selecteer en implementeer een STONITH-oplossing.  Mogelijke opties: Azure Fencing Agent (ondersteund voor zowel [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker), [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)), SBD |
| 6 | [HA met HSR, DR met opslagreplicatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication) | Nee | Opslagreplicatie vervangen voor DR-behoeften door HSR of back-up/herstel |
| 7 | [Host automatische failover (1+1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11) | Ja | ANF gebruiken voor gedeelde opslag met Azure VM's |
| 8 | [Scale-out met stand-by](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby) | Ja | BW/4HANA met M128's, M416's, M416ms VM's met alleen ANF voor opslag |
| 9 | [Scale-out zonder stand-by](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby) | Ja | BW/4HANA met M128's, M416's, M416ms VM's (met of zonder ANF te gebruiken voor opslag) |
| 10 | [Uitschalen met DR met opslagreplicatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication) | Nee | Opslagreplicatie vervangen voor DR-behoeften door HSR of back-up/herstel |
| 11 | [Eén knooppunt met DR met HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr) | Ja | - |
| 12 | [Single node HSR naar DR (kosten geoptimaliseerd)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized) | Ja | - |
| 13 | [HA en DR met HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr) | Ja | - |
| 14 | [HA en DR met HSR (kosten geoptimaliseerd)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Ja | - |
| 15 | [Scale-out met DR met HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr) | Ja | BW/4HANA met M128's. M416's, Vm's van M416ms (met of zonder ANF te gebruiken voor opslag) |


## <a name="source-hli-planning"></a>Bron (HLI) planning
Bij het inwerken van een HLI-server hebben zowel Microsoft Service Management als klanten de planning van de compute-, netwerk-, opslag- en OS-specifieke instellingen voor het uitvoeren van de SAP HANA-database doorlopen.  Vergelijkbare planning moet plaatsvinden voor de migratie naar Azure VM.

### <a name="sap-hana-housekeeping"></a>SAP HANA huishouding 
Het is een goede operationele praktijk om de database-inhoud op te ruimen, zodat ongewenste, verouderde gegevens of verouderde logboeken niet worden gemigreerd naar de nieuwe database.  Het huishouden omvat over het algemeen het verwijderen of archiveren van oude, verlopen of inactieve gegevens.  Deze acties op het gebied van gegevenshygiëne moeten worden getest in niet-productiesystemen om de geldigheid van hun gegevenstrim vóór het productiegebruik te valideren.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Netwerkconnectiviteit toestaan voor nieuwe VM's en of een virtueel netwerk 
In de HLI-implementatie van een klant is het netwerk ingesteld op basis van de informatie die wordt beschreven in de netwerkarchitectuur van [SAP HANA (Large Instances).](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture) Ook wordt het routeren van netwerkverkeer uitgevoerd op de manier die wordt beschreven in de sectie 'Routering in Azure'.
- Bij het instellen van een nieuwe VM als migratiedoel is als het in het bestaande virtuele netwerk met IP-adresbereiken al toegestaan om verbinding te maken met de HLI, geen verdere verbindingsupdate vereist.
- Als de nieuwe Azure VM in een nieuw Microsoft Azure Virtual Network wordt geplaatst, zich mogelijk in een andere regio bevindt en is gekoppeld aan het bestaande virtuele netwerk, zijn de ExpressRoute-servicesleutel en Resource-id van de oorspronkelijke HLI-inrichting bruikbaar om toegang te verlenen voor deze nieuwe virtuele netwerk-IP-bereik.  Coördineer met Microsoft Service Management om het virtuele netwerk in te schakelen voor HLI-connectiviteit.  Opmerking: Om de netwerklatentie tussen de toepassings- en databaselagen te minimaliseren, moeten zowel de toepassings- als databaselagen zich op hetzelfde virtuele netwerk bevinden.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>Bestaande beschikbaarheidsset voor app-lagen, beschikbaarheidszones en nabijheidsplaatsingsgroep (PPG)
Het huidige implementatiemodel wordt uitgevoerd om te voldoen aan bepaalde doelstellingen op serviceniveau.  Zorg er in deze beweging voor dat de doelinfrastructuur de gestelde doelen haalt of overschrijdt.  
Meer waarschijnlijk dan niet, klanten SAP applicatie servers worden geplaatst in een beschikbaarheid set.  Als het huidige implementatieserviceniveau bevredigend is en 
- Als de doel-VM de hostnaam van de logische NAAM van DE HLI aanneemt, zou het bijwerken van de adresomzetting van de domeinnaamservice (DNS) die naar het IP-adres van de VM wijst, werken zonder dat SAP-profielen worden bijgewerkt
- Als u PPG niet gebruikt, moet u alle toepassing- en DB-servers in dezelfde zone plaatsen om de netwerklatentie te minimaliseren.
- Als u PPG gebruikt, raadpleegt u het gedeelte van dit document: 'Bestemmingsplanning, beschikbaarheidsset, beschikbaarheidszones en nabijheidsplaatsingsgroep (PPG)'.

### <a name="storage-replication-discontinuance-process-if-used"></a>Replicatiereplicatiereplicatieproces (indien gebruikt)
Als opslagreplicatie wordt gebruikt als de DR-oplossing, moet deze worden beëindigd (niet gepland) nadat de SAP-toepassing is afgesloten.  Bovendien zijn de laatste SAP HANA-catalogus, logboekbestand en gegevensback-ups gerepliceerd naar de externe DR HLI-opslagvolumes.  Dit als voorzorgsmaatregel in het geval er zich een ramp voordoet tijdens de fysieke overgang van de server naar Azure VM.

### <a name="data-backups-preservation-consideration"></a>Rekening houden met het bewaren van back-ups van gegevens
Na de cut-over naar SAP HANA op Azure VM zijn alle op momentopnamen gebaseerde gegevens of logboekback-ups op de HLI niet gemakkelijk toegankelijk of herstelbaar voor een VM indien nodig. In de vroege overgangsperiode, voordat de azure-gebaseerde back-up voldoende geschiedenis bouwt om te voldoen aan point-in-time-herstelvereisten, raden we u aan om back-ups op bestandsniveau te maken naast momentopnamen op de HLI, dagen of weken voor de cut-over.  Laat deze back-ups kopiëren naar een Azure Storage-account dat toegankelijk is voor de nieuwe SAP HANA VM.
Naast het maken van back-ups van de HLI-inhoud, is het verstandig om volledige back-ups van het SAP-landschap gemakkelijk toegankelijk te hebben voor het geval een terugdraaiing nodig is.

### <a name="adjusting-system-monitoring"></a>Systeembewaking aanpassen 
Klanten gebruiken veel verschillende tools om waarschuwingsmeldingen te controleren en te verzenden voor systemen binnen hun SAP-landschap.  Dit item is slechts een oproep voor passende actie om wijzigingen op te nemen voor het bewaken en bijwerken van de ontvangers van waarschuwingen indien nodig.

### <a name="microsoft-operations-team-involvement"></a>Betrokkenheid van het Microsoft Operations-team 
Open een ticket vanaf de Azure-portal op basis van het bestaande HLI-exemplaar.  Nadat het ondersteuningsticket is gemaakt, neemt een ondersteuningstechnicus contact met u op via e-mail.  

### <a name="engage-microsoft-account-team"></a>Microsoft-accountteam inschakelen
Plan migratie in de buurt van de verlengingstijd van het HLI-contract om onnodige kosten voor compute resource te minimaliseren. Om het HLI-blad te ontmantelen, is het nodig om de beëindiging van het contract en de daadwerkelijke uitschakeling van het apparaat te coördineren.

## <a name="destination-planning"></a>Bestemmingsplanning
Het opstaan van een nieuwe infrastructuur om de plaats in te nemen van een bestaande verdient enige denkwijze om ervoor te zorgen dat de nieuwe toevoeging past in het grote schema van de dingen.  Hieronder vindt u enkele belangrijke punten voor contemplatie.

### <a name="resource-availability-in-the-target-region"></a>Beschikbaarheid van resources in de doelregio 
De implementatieregio van de huidige SAP-toepassingsservers bevindt zich doorgaans in de nabijheid van de bijbehorende HLI's.  HLI's worden echter op minder locaties aangeboden dan beschikbare Azure-regio's.  Bij het migreren van de fysieke HLI naar Azure VM is het ook een goed moment om de nabijheidsafstand van alle gerelateerde services voor prestatieoptimalisatie te 'finetunen'.  Daarbij is een belangrijke overweging om ervoor te zorgen dat de gekozen regio over alle benodigde middelen beschikt.  Bijvoorbeeld de beschikbaarheid van bepaalde VM-familie of het aanbieden van Azure Zones voor het instellen van hoge beschikbaarheid.

### <a name="virtual-network"></a>Virtueel netwerk 
Klanten moeten kiezen of ze de nieuwe HANA-database in een bestaand virtueel netwerk willen uitvoeren of een nieuwe database willen maken.  De belangrijkste beslissende factor is de huidige netwerkindeling voor het SAP-landschap.  Ook wanneer de infrastructuur gaat van een-zone naar twee-zones inzet en maakt gebruik van PPG, het legt architectonische verandering. Zie het artikel [Azure PPG voor optimale netwerklatentie met SAP-toepassing voor](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)meer informatie.   

### <a name="security"></a>Beveiliging
Of de nieuwe SAP HANA VM nu op een nieuw of bestaand vnet/subnet landt, het vertegenwoordigt een nieuwe bedrijfskritieke service die bescherming vereist.  Toegangscontrole die voldoet aan het beveiligingsbeleid voor bedrijfsgegevens moet worden geëvalueerd en geïmplementeerd voor deze nieuwe serviceklasse.

### <a name="vm-sizing-recommendation"></a>Aanbeveling voor vm-dimensionering
Deze migratie is ook een kans om de juiste grootte van uw HANA compute engine.  Men kan hana [systeemmeningen](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) in combinatie met HANA Studio gebruiken om het systeembronverbruik te begrijpen, dat voor juiste grootte toestaat om uitgavenefficiency te drijven.

### <a name="storage"></a>Storage 
Opslagprestaties zijn een van de factoren die van invloed zijn op de gebruikerservaring van SAP-toepassingen.  Op basis van een bepaalde VM SKU, zijn er minimale opslaglay-out gepubliceerd [SAP HANA Azure virtuele machine opslag configuraties](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). We raden u aan deze minimale specificaties te bekijken en te vergelijken met de bestaande HLI-systeemstatistieken om voldoende IO-capaciteit en -prestaties voor de nieuwe HANA VM te garanderen.

Als u PPG configureert voor de nieuwe HANA VM en de bijbehorende severs, dient u een ondersteuningsticket in om de colocatie van de opslag en de VM te inspecteren en te garanderen. Aangezien uw back-upoplossing mogelijk moet worden gewijzigd, moeten de opslagkosten ook opnieuw worden bekeken om verrassingen van operationele uitgaven te voorkomen.

### <a name="storage-replication-for-disaster-recovery"></a>Opslagreplicatie voor herstel na noodgevallen
Met HLI werd opslagreplicatie aangeboden als de standaardoptie voor het herstel na noodgevallen. Deze functie is niet de standaardoptie voor SAP HANA op Azure VM. Denk aan HSR, back-up/restore of andere ondersteunde oplossingen die voldoen aan uw bedrijfsbehoeften.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Beschikbaarheidssets, beschikbaarheidszones en plaatsingsgroepen voor nabijheid 
Om de afstand tussen de toepassingslaag en SAP HANA te verkorten om de netwerklatentie tot een minimum te beperken, moeten de nieuwe database VM en de huidige SAP-toepassingsservers in een PPG worden geplaatst. Raadpleeg [proximity placementgroep](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) voor meer informatie over hoe Azure Availability Set and Availability Zones werken met PPG voor SAP-implementaties.
Als leden van het doel-HANA-systeem in meer dan één Azure-zone worden geïmplementeerd, moeten klanten een duidelijk beeld hebben van het latentieprofiel van de gekozen zones. De plaatsing van SAP-systeemcomponenten is optimaal met betrekking tot de proximale afstand tussen SAP-toepassing en de database.  Het [hulpprogramma voor de latentietest voor de beschikbaarheidszone](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) van het publieke domein maakt de meting eenvoudiger.  


### <a name="backup-strategy"></a>Back-upstrategie
Veel klanten maken al gebruik van back-upoplossingen van derden voor SAP HANA op HLI.  In dat geval hoeft alleen een extra beveiligde VM- en HANA-databases te worden geconfigureerd.  Lopende HLI-back-uptaken kunnen nu worden ongepland als de machine na de migratie buiten gebruik wordt gesteld.
Azure Backup voor SAP HANA op VM is nu algemeen beschikbaar.  Zie deze koppelingen voor gedetailleerde informatie over: [Back-up,](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) [Herstel](https://docs.microsoft.com/azure/backup/sap-hana-db-restore), SAP HANA-back-up [beheren](https://docs.microsoft.com/azure/backup/sap-hana-db-manage) in Azure VM's.

### <a name="dr-strategy"></a>DR-strategie
Als uw serviceniveaudoelstellingen geschikt zijn voor een langere hersteltijd, is een eenvoudige back-up naar blobopslag en herstel op zijn plaats of herstellen naar een nieuwe VM de eenvoudigste en goedkoopste DR-strategie.  
Net als het grote instantieplatform waar HANA DR meestal wordt gedaan met HSR; Op Azure VM is HSR ook de meest natuurlijke en native SAP HANA DR-oplossing.  Ongeacht of de bronimplementatie één instantie of geclusterd is, is een replica van de broninfrastructuur vereist in het DR-gebied.
Deze DR-replica wordt geconfigureerd nadat de primaire HLI-naar-VM-migratie is voltooid.  De DR HANA DB registreert zich bij de primaire SAP HANA op VM-instantie als secundaire replicatiesite.  

### <a name="sap-application-server-connectivity-destination-change"></a>Wijziging van de doelbestemming van de SAP-toepassingsserver-connectiviteit
De HSR-migratie resulteert in een nieuwe HANA DB-host host en dus een nieuwe DB-hostnaam voor de toepassingslaag, SAP-profielen moeten worden aangepast aan de nieuwe hostnaam.  Als de omschakeling wordt uitgevoerd door naamresolutie met behoud van de hostnaam, is er geen profielwijziging vereist.

### <a name="operating-system"></a>Besturingssysteem
De afbeeldingen van het besturingssysteem voor HLI en VM, ondanks het feit dat op hetzelfde releaseniveau, SLES 12 SP4 bijvoorbeeld, zijn niet identiek. Klanten moeten de vereiste pakketten, hot fixes, patches, kernel en beveiligingsoplossingen op de HLI valideren om dezelfde pakketten op het doel te installeren.  Het wordt ondersteund om HSR te gebruiken om te repliceren van een ouder besturingssysteem op een VM met een nieuwere OS-versie.  Controleer de specifieke ondersteunde versies door [SAP note 2763388](https://launchpad.support.sap.com/#/notes/2763388)te bekijken .

### <a name="new-sap-license-request"></a>Nieuwe SAP-licentieaanvraag
Een eenvoudige oproep om een nieuwe SAP-licentie aan te vragen voor het nieuwe HANA-systeem nu het is gemigreerd naar VM's.

### <a name="service-level-agreement-sla-differences"></a>VERSCHILLEN in serviceniveauovereenkomst (SLA)
De auteurs roepen graag het verschil van beschikbaarheid SLA tussen HLI en Azure VM.  Geclusterde HLI's HA-paren bieden bijvoorbeeld 99,99% beschikbaarheid. Om dezelfde SLA te bereiken, moet men VM's implementeren in beschikbaarheidszones. In [dit artikel](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) wordt de beschikbaarheid met bijbehorende implementatiearchitecturen beschreven, zodat klanten hun doelinfrastructuur dienovereenkomstig kunnen plannen.


## <a name="migration-strategy"></a>Migratiestrategie
In dit document behandelen we alleen de HANA-systeemreplicatiebenadering voor de migratie van HLI naar Azure VM.  Afhankelijk van de geïmplementeerde doelopslagoplossing verschilt het proces enigszins. De stappen op hoog niveau worden hieronder beschreven.

### <a name="vm-with-premiumultra-disks-for-data"></a>VM met premium/ultra-schijven voor gegevens
Voor VM's die worden geïmplementeerd met premium- of ultraschijven, is de standaard SAP HANA-systeemreplicatieconfiguratie van toepassing voor het instellen van HSR.  Het [SAP-help-artikel](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) geeft een overzicht van de stappen die nodig zijn bij het instellen van systeemreplicatie, het overnemen van een secundair systeem, het niet terugnaar de primaire en uitschakelen van systeemreplicatie.  Voor de migratie hebben we alleen de installatie nodig, die replicatiestappen overneemt en uitschakelt.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>VM met ANF voor gegevens- en logboekvolumes
Op een hoog niveau moeten de nieuwste HLI-opslagmomentopnamen van de volledige gegevens- en logboekvolumes worden gekopieerd naar Azure Storage, waar ze toegankelijk en herstelbaar zijn door de beoogde HANA VM.  Het kopieerproces kan worden gedaan met elke native Linux copy tools.  

> [!IMPORTANT]
> Kopiëren en gegevensoverdracht kan uren duren, afhankelijk van de hana-databasegrootte en netwerkbandbreedte.  Het grootste deel van het kopieerproces moet worden gedaan voorafgaand aan de primaire HANA DB downtime.

### <a name="mcos-to-mdc-conversion"></a>MCOS naar MDC-conversie
Het implementatiemodel Multiple Components in One System (MCOS) werd gebruikt door een aantal van onze HLI-klanten.  De motivatie was om de Multiple Databases Container (MDC) storage snapshot beperking van eerdere SAP HANA versies te omzeilen.  In het MCOS-model worden verschillende onafhankelijke SAP HANA-exemplaren opgestapeld in één HLI-blad.  Het gebruik van HSR voor de migratie zou prima werken, maar resulteert in meerdere HANA VM's met één tenant DB per stuk.  Deze eindstatus zorgt voor een drukker landschap dan waar een klant misschien aan gewend was.  Met SAP HANA 2.0 standaard implementatie wordt MDC, Een levensvatbaar alternatief is het uitvoeren [van HANA tenant verplaatsen](https://launchpad.support.sap.com/#/notes/2472478) na de HSR migratie.  Dit proces 'consolideert' deze onafhankelijke HANA databases in medehuurders in één HANA container.  

### <a name="application-layer-consideration"></a>Toepassinglaagoverweging
De DB-server wordt gezien als het centrum van een SAP-systeem.  Alle applicatieservers moeten zich in de buurt van de SAP HANA DB bevinden.  In sommige gevallen wanneer nieuw gebruik van PPG gewenst is, kan het mogelijk zijn bestaande applicatieservers naar de PPG te verplaatsen waar de HANA VM is.  Het bouwen van nieuwe toepassingsservers kan eenvoudiger worden geacht als u al implementatiesjablonen bij de hand hebt.  
Als bestaande toepassingsservers en de nieuwe HANA VM optimaal zijn gelokaliseerd, hoeven er geen nieuwe toepassingsservers te worden gebouwd, tenzij extra capaciteit vereist is.  
Als een nieuwe infrastructuur is gebouwd om de beschikbaarheid van de service te verbeteren, kunnen de bestaande toepassingsservers overbodig worden en moeten ze worden afgesloten en verwijderd.
Als de status van de doel-VM-host is gewijzigd en verschilt van de HLI-hostnaam, moeten SAP-toepassingsserverprofielen worden aangepast om naar de nieuwe host te wijzen.  Als alleen het IP-adres van de HANA DB is gewijzigd, is een DNS-recordupdate nodig om binnenkomende verbindingen naar de nieuwe HANA VM te leiden.

### <a name="acceptance-test"></a>Acceptatietest
Hoewel de migratie van HLI naar VM geen wezenlijke wijzigingen aanbrengt in de database-inhoud in vergelijking met een heterogene migratie, raden we u nog steeds aan de belangrijkste functionaliteiten en prestatieaspecten van de nieuwe installatie te valideren.  

### <a name="cutover-plan"></a>Cutover plan
Hoewel deze migratie eenvoudig is, gaat het echter om de ontmanteling van een bestaande DB.  Zorgvuldige planning om het bronsysteem te behouden met de bijbehorende inhoud en back-upafbeeldingen zijn van cruciaal belang voor het geval terugval nodig is.  Goede planning biedt een snellere omkering.   


## <a name="post-migration"></a>Na de migratie
De migratietaak wordt pas uitgevoerd nadat we hli-afhankelijke services of connectiviteit veilig hebben losgekoppeld om ervoor te zorgen dat de gegevensintegriteit behouden blijft.  Ook, stilgelegd onnodige diensten.  Deze sectie roept een paar top-of-mind items.

### <a name="decommissioning-the-hli"></a>Ontmanteling van de HLI
Na een succesvolle migratie van de HANA DB naar Azure VM, moet u ervoor zorgen dat er geen productieve zakelijke transacties worden uitgevoerd op de HLI DB.  Echter, het houden van de HLI draait voor een periode van tijd is gelijk aan de lokale back-up retentie venster is een veilige praktijk zorgen voor een snellere herstel indien nodig.  Alleen dan moet het HLI-mes worden ontmanteld.  Klanten moeten contractueel hun HLI-verbintenissen met Microsoft sluiten door contact op te nemen met hun Microsoft-vertegenwoordigers.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Elke proxy verwijderen (bijvoorbeeld Iptables, BIGIP) die is geconfigureerd voor HLI 
Als een proxyservice zoals de IPTables wordt gebruikt om on-premises verkeer van en naar de HLI te routeren, is deze niet meer nodig na de succesvolle migratie naar VM.  Deze connectiviteitsservice moet echter worden bewaard zolang het HLI-blad nog steeds stand-by is.  Sluit de service alleen af nadat het HLI-mes volledig buiten gebruik is gesteld.

### <a name="remove-global-reach-for-hli"></a>Globaal bereik voor HLI verwijderen 
Global Reach wordt gebruikt om de ExpressRoute-gateway van klanten te verbinden met de HLI ExpressRoute-gateway.  Het stelt het on-premises verkeer van klanten in staat om de HLI-tenant rechtstreeks te bereiken zonder het gebruik van een proxyservice.  Deze verbinding is niet langer nodig zonder de HLI-eenheid na migratie.  Net als bij de IPTables-proxyservice moet GlobalReach ook worden bewaard totdat het HLI-blad volledig buiten gebruik is gesteld.

### <a name="operating-system-subscription--movereuse"></a>Abonnement op het besturingssysteem – verplaatsen/hergebruiken
Als de VM-servers worden opgestaan en de HLI-blades worden ontmanteld, kunnen de OS-abonnementen worden vervangen of hergebruikt om dubbel betalen van OS-licenties te voorkomen.



## <a name="next-steps"></a>Volgende stappen
Zie deze artikelen:
- [SAP HANA-infrastructuurconfiguraties en -bewerkingen op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
- [SAP-workloads op Azure: checklist planning en implementatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist).
