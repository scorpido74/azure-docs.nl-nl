---
title: Onderdelen en grenzen van azure-informatiesysteem
description: In dit artikel vindt u een algemene beschrijving van de Microsoft Azure-architectuur en -beheer.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 68535f70507e7a81d217f4148314a3d76ec832ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727203"
---
# <a name="azure-information-system-components-and-boundaries"></a>Onderdelen en grenzen van azure-informatiesysteem
In dit artikel vindt u een algemene beschrijving van de Azure-architectuur en -beheer. De Azure-systeemomgeving bestaat uit de volgende netwerken:

- Microsoft Azure-productienetwerk (Azure-netwerk)
- Microsoft bedrijfsnetwerk (corpnet)

Aparte IT-teams zijn verantwoordelijk voor de exploitatie en het onderhoud van deze netwerken.

## <a name="azure-architecture"></a>Azure-architectuur
Azure is een cloud computing-platform en -infrastructuur voor het bouwen, implementeren en beheren van applicaties en services via een netwerk van datacenters. Microsoft beheert deze datacenters. Op basis van het aantal resources dat u opgeeft, maakt Azure virtuele machines (VM's) op basis van de behoefte aan resources. Deze VM's worden uitgevoerd op een Azure-hypervisor, die is ontworpen voor gebruik in de cloud en niet toegankelijk is voor het publiek.

Op elk extern knooppunt van de fysieke server is er een hypervisor die rechtstreeks over de hardware draait. De hypervisor verdeelt een knooppunt in een variabel aantal gastVM's. Elk knooppunt heeft ook één root-VM, waarop het host-besturingssysteem wordt uitgevoerd. Windows Firewall is ingeschakeld op elke virtuele machine. U definieert welke poorten adresseerbaar zijn door het servicedefinitiebestand te configureren. Deze poorten zijn de enigen die open en adresseerbaar zijn, intern of extern. Al het verkeer en de toegang tot de schijf en het netwerk wordt bemiddeld door de hypervisor en root besturingssysteem.

Op de hostlaag draaien Azure VM's een aangepaste en geharde versie van de nieuwste Windows Server. Azure gebruikt een versie van Windows Server die alleen de onderdelen bevat die nodig zijn om VM's te hosten. Dit verbetert de prestaties en vermindert het aanvalsoppervlak. Machinegrenzen worden afgedwongen door de hypervisor, die niet afhankelijk is van de beveiliging van het besturingssysteem.

### <a name="azure-management-by-fabric-controllers"></a>Azure-beheer door fabriccontrollers

In Azure worden VM's die op fysieke servers (blades/nodes) worden uitgevoerd, gegroepeerd in clusters van ongeveer 1000. De VM's worden onafhankelijk beheerd door een geschaald en redundant platform software component genaamd de stof controller (FC).

Elke FC beheert de levenscyclus van toepassingen die in het cluster worden uitgevoerd, en voorziet en bewaakt de status van de hardware onder zijn controle. Er worden autonome bewerkingen uitgevoerd, zoals het opnieuw incarneren van VM-exemplaren op gezonde servers wanneer wordt vastgesteld dat een server is mislukt. De FC voert ook toepassingsbeheerbewerkingen uit, zoals het implementeren, bijwerken en uitschalen van toepassingen.

Het datacenter is verdeeld in clusters. Clusters isoleren fouten op FC-niveau en voorkomen dat bepaalde klassen van fouten van invloed zijn op servers buiten het cluster waarin ze zich voordoen. FCs die een bepaald Azure-cluster dienen, worden gegroepeerd in een FC-cluster.

### <a name="hardware-inventory"></a>Hardware-inventaris

De FC bereidt een inventaris van Azure-hardware en netwerkapparaten voor tijdens het configuratieproces van bootstrap. Nieuwe hardware- en netwerkonderdelen die de Azure-productieomgeving binnenkomen, moeten het configuratieproces bootstrap volgen. De FC is verantwoordelijk voor het beheer van de volledige voorraad die wordt vermeld in het configuratiebestand datacenter.xml.

### <a name="fc-managed-operating-system-images"></a>FC-beheerde besturingssysteembeelden

Het besturingssysteemteam levert afbeeldingen, in de vorm van virtuele harde schijven, geïmplementeerd op alle host- en gast-VM's in de Azure-productieomgeving. Het team construeert deze basisafbeeldingen via een geautomatiseerd offline bouwproces. De basisafbeelding is een versie van het besturingssysteem waarin de kernel en andere kerncomponenten zijn gewijzigd en geoptimaliseerd om de Azure-omgeving te ondersteunen.

Er zijn drie soorten stofbeheerde besturingssysteemafbeeldingen:

- Host: een aangepast besturingssysteem dat wordt uitgevoerd op host-VM's.
- Native: een native besturingssysteem dat wordt uitgevoerd op tenants (bijvoorbeeld Azure Storage). Dit besturingssysteem heeft geen hypervisor.
- Gast: Een gastbesturingssysteem dat draait op vm's van gasten.

De host- en native FC-beheerde besturingssystemen zijn ontworpen voor gebruik in de cloud en zijn niet openbaar toegankelijk.

#### <a name="host-and-native-operating-systems"></a>Host- en native besturingssystemen

Host en native zijn geharde besturingssysteemafbeeldingen die de fabricagents hosten en worden uitgevoerd op een compute node (wordt uitgevoerd als eerste VM op het knooppunt) en opslagknooppunten. Het voordeel van het gebruik van geoptimaliseerde basisafbeeldingen van host en native is dat het de oppervlakte die wordt blootgesteld door API's of ongebruikte componenten vermindert. Deze kunnen hoge veiligheidsrisico's met zich meebrengen en de voetafdruk van het besturingssysteem vergroten. Besturingssystemen met een beperkte voetafdruk bevatten alleen de componenten die nodig zijn voor Azure.

#### <a name="guest-operating-system"></a>Gastbesturingssysteem

Azure-interne componenten die worden uitgevoerd op vm's van gastbesturingssystemen, hebben geen mogelijkheid om Extern bureaublad-protocol uit te voeren. Wijzigingen in de basislijnconfiguratie-instellingen moeten het wijzigings- en releasebeheerproces doorlopen.

## <a name="azure-datacenters"></a>Azure-datacenters
Het MCIO-team (Microsoft Cloud Infrastructure and Operations) beheert de fysieke infrastructuur en datacenterfaciliteiten voor alle online services van Microsoft. MCIO is primair verantwoordelijk voor het beheer van de fysieke en milieucontroles in de datacenters, evenals het beheren en ondersteunen van externe perimeter netwerkapparaten (zoals edge routers en datacenter routers). MCIO is ook verantwoordelijk voor het opzetten van de absolute minimale serverhardware op racks in het datacenter. Klanten hebben geen directe interactie met Azure.

## <a name="service-management-and-service-teams"></a>Service management en service teams
Verschillende engineeringgroepen, ook wel serviceteams genoemd, beheren de ondersteuning van de Azure-service. Elk serviceteam is verantwoordelijk voor een ondersteuningsgebied voor Azure. Elk serviceteam moet 24 x 7 een technicus beschikbaar stellen om fouten in de service te onderzoeken en op te lossen. Serviceteams hebben standaard geen fysieke toegang tot de hardware die in Azure werkt.

De serviceteams zijn:

- Toepassingsplatform
- Azure Active Directory
- Azure Compute
- Azure-net
- Cloud Engineering Services
- ISSD: Beveiliging
- Multifactorauthenticatie
- SQL Database
- Storage

## <a name="types-of-users"></a>Typen gebruikers
Werknemers (of contractanten) van Microsoft worden beschouwd als interne gebruikers. Alle andere gebruikers worden beschouwd als externe gebruikers. Alle interne Azure-gebruikers hebben hun werknemersstatus gecategoriseerd met een gevoeligheidsniveau dat hun toegang tot klantgegevens definieert (toegang of geen toegang). Gebruikersrechten voor Azure (autorisatiemachtiging na verificatie) worden beschreven in de volgende tabel:

| Rol | Intern of extern | Gevoeligheidsniveau | Geautoriseerde bevoegdheden en functies uitgevoerd | Toegangstype
| --- | --- | --- | --- | --- |
| Azure-datacenterengineer | Intern | Geen toegang tot klantgegevens | Beheer de fysieke beveiliging van het pand. Voer patrouilles uit in en uit het datacenter en controleer alle toegangspunten. Escorteer in en uit het datacenter bepaalde niet-gecleard personeel dat algemene diensten levert (zoals dineren of schoonmaken) of IT-werk binnen het datacenter. Uitvoeren van routinemonitoring en onderhoud van netwerkhardware. Voer incidentbeheer en break-fix werk uit met behulp van verschillende tools. Voer routinematige monitoring en onderhoud uit van de fysieke hardware in de datacenters. Toegang tot de omgeving op aanvraag van eigenaren van onroerend goed. Geschikt om forensisch onderzoek uit te voeren, incidentrapporten te registreren en verplichte beveiligingstraining en beleidsvereisten te vereisen. Operationele eigendom en onderhoud van kritieke beveiligingstools, zoals scanners en logboekverzameling. | Permanente toegang tot het milieu. |
| Azure incident triage (rapid response engineers) | Intern | Toegang tot klantgegevens | Beheer de communicatie tussen MCIO-, ondersteunings- en engineeringteams. Triage-platformincidenten, implementatieproblemen en serviceaanvragen. | Just-in-time toegang tot de omgeving, met beperkte permanente toegang tot niet-klantsystemen. |
| Azure-implementatietechnici | Intern | Toegang tot klantgegevens | Implementeren en upgraden van platformonderdelen, software en geplande configuratiewijzigingen ter ondersteuning van Azure. | Just-in-time toegang tot de omgeving, met beperkte permanente toegang tot niet-klantsystemen. |
| Azure-ondersteuning voor klantuitval (tenant) | Intern | Toegang tot klantgegevens | Foutopsporing en diagnose van platformstoringen en -fouten voor afzonderlijke rekentenants en Azure-accounts. Analyseer fouten. Stimuleer kritieke oplossingen voor het platform of de klant en stimuleer technische verbeteringen voor alle ondersteuning. | Just-in-time toegang tot de omgeving, met beperkte permanente toegang tot niet-klantsystemen. |
| Azure live site engineers (monitoring engineers) en incident | Intern | Toegang tot klantgegevens | Diagnosticeer en verminder de status van het platform met behulp van diagnostische tools. Stationfixes voor volumestuurprogramma's, reparatievan items als gevolg van uitval en ondersteuning van uitvalherstelacties. | Just-in-time toegang tot de omgeving, met beperkte permanente toegang tot niet-klantsystemen. |
|Azure-klanten | Extern | N.v.t. | N.v.t. | N.v.t. |

Azure gebruikt unieke id's om gebruikers en klanten van organisaties te verifiëren (of processen die namens organisatiegebruikers optreden). Dit geldt voor alle elementen en apparaten die deel uitmaken van de Azure-omgeving.

### <a name="azure-internal-authentication"></a>Interne azure-verificatie

Communicatie tussen interne Azure-componenten wordt beveiligd met TLS-versleuteling. In de meeste gevallen zijn de X.509-certificaten zelf ondertekend. Certificaten met verbindingen die van buiten het Azure-netwerk toegankelijk zijn, zijn een uitzondering, evenals certificaten voor de FCs. SC's hebben certificaten die zijn uitgegeven door een Microsoft Certificate of Authority (CA) die wordt ondersteund door een vertrouwde hoofd-CA. Hierdoor kunnen fc publieke sleutels gemakkelijk worden gerold over. Daarnaast gebruiken microsoft-hulpprogramma's voor ontwikkelaars openbare sleutels van FC. Wanneer ontwikkelaars nieuwe toepassingsafbeeldingen indienen, worden de afbeeldingen versleuteld met een openbare FC-sleutel om ingesloten geheimen te beschermen.

### <a name="azure-hardware-device-authentication"></a>Azure-hardwareapparaatverificatie

De FC onderhoudt een set referenties (sleutels en/of wachtwoorden) die worden gebruikt om zichzelf te authenticeren naar verschillende hardwareapparaten onder haar controle. Microsoft gebruikt een systeem om toegang tot deze referenties te voorkomen. Met name het transport, de persistentie en het gebruik van deze referenties is ontworpen om azure-ontwikkelaars, beheerders en back-upservices en personeel toegang te bieden tot gevoelige, vertrouwelijke of privégegevens.

Microsoft maakt gebruik van encryptie op basis van de openbare sleutel van de fc-masteridentiteit. Dit gebeurt bij FC setup en FC herconfiguratie tijden, om de referenties die worden gebruikt om toegang te krijgen tot netwerkhardware apparaten over te dragen. Wanneer de FC de referenties nodig heeft, haalt en ontsleutelt de FC ze.

### <a name="network-devices"></a>Netwerkapparaten

Het Azure-netwerkteam configureert netwerkserviceaccounts om een Azure-client te kunnen verifiëren voor netwerkapparaten (routers, switches en load balancers).

## <a name="secure-service-administration"></a>Beveiligde servicebeheer
Azure-operations-personeel is verplicht om veilige beheerwerkstations (SAW's) te gebruiken. Klanten kunnen vergelijkbare besturingselementen implementeren met behulp van geprivilegieerde werkstations met toegang. Bij SAW's gebruiken administratief personeel een individueel toegewezen administratief account dat los staat van het standaardgebruikersaccount van de gebruiker. De SAW bouwt voort op die accountscheidingspraktijk door een betrouwbaar werkstation te bieden voor die gevoelige accounts.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over wat Microsoft doet om de Azure-infrastructuur te beveiligen:

- [Azure-faciliteiten, lokalen en fysieke beveiliging](physical-security.md)
- [Beschikbaarheid azure-infrastructuur](infrastructure-availability.md)
- [Azure-netwerkarchitectuur](infrastructure-network.md)
- [Azure-productienetwerk](production-network.md)
- [Beveiligingsfuncties van Azure SQL Database](infrastructure-sql.md)
- [Azure-productiebewerkingen en -beheer](infrastructure-operations.md)
- [Azure-infrastructuurbewaking](infrastructure-monitoring.md)
- [Integriteit van Azure-infrastructuur](infrastructure-integrity.md)
- [Azure-klantgegevensbeveiliging](protection-customer-data.md)
