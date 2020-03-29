---
title: Beveiligingsfuncties van Azure SQL Database
description: In dit artikel vindt u een algemene beschrijving van hoe Azure SQL Database klantgegevens in Azure beschermt.
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
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: ad6d3992f03802174eb03aa30b57b8d3dac1d6c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942960"
---
# <a name="azure-sql-database-security-features"></a>Beveiligingsfuncties van Azure SQL Database    
Azure SQL Database biedt een relationele databaseservice in Azure. Om klantgegevens te beschermen en sterke beveiligingsfuncties te bieden die klanten verwachten van een relationele databaseservice, beschikt SQL Database over zijn eigen beveiligingsmogelijkheden. Deze mogelijkheden bouwen voort op de besturingselementen die zijn overgenomen van Azure.

## <a name="security-capabilities"></a>Beveiligingsmogelijkheden

### <a name="usage-of-the-tds-protocol"></a>Gebruik van het TDS-protocol
Azure SQL Database ondersteunt alleen het TDS-protocol (Tabular Data Stream), waarvoor de database alleen toegankelijk moet zijn via de standaardpoort van TCP/1433.

### <a name="azure-sql-database-firewall"></a>Azure SQL Database-firewall
Om klantgegevens te beschermen, bevat Azure SQL Database een firewallfunctionaliteit, die standaard alle toegang tot de SQL Database-server verhindert, zoals hieronder wordt weergegeven.

![Azure SQL Database-firewall](./media/infrastructure-sql/sql-database-firewall.png)

De gatewayfirewall kan adressen beperken, waardoor klanten gedetailleerde controle kunnen beheren om bereiken van aanvaardbare IP-adressen op te geven. De firewall verleent toegang op basis van het ip-adres van elke aanvraag.

Klanten kunnen firewallconfiguratie bereiken door een beheerportal te gebruiken of programmatisch gebruik te maken van de Azure SQL Database Management REST API. De Azure SQL Database-gatewayfirewall voorkomt standaard dat alle TDS-apparaten van de klant toegang krijgen tot Azure SQL-database-exemplaren. Klanten moeten toegang configureren met behulp van toegangscontrolelijsten (ACL's) om Azure SQL Database-verbindingen toe te staan via bron- en doelinternetadressen, protocollen en poortnummers.

### <a name="dosguard"></a>DoSGuard DoSGuard
DoS-aanvallen (Denial of service) worden verminderd door een SQL Database-gatewayservice genaamd DoSGuard. DoSGuard volgt actief mislukte aanmeldingen van IP-adressen. Als er binnen een bepaalde periode meerdere mislukte aanmeldingen van een specifiek IP-adres zijn, is het IP-adres geblokkeerd om gedurende een vooraf bepaalde periode toegang te krijgen tot bronnen in de service.

Bovendien voert de Azure SQL Database-gateway het als:

- Onderhandelingen over veilige kanaalmogelijkheden om gevalideerde versleutelde verbindingen van TDS FIPS 140-2 te implementeren wanneer deze verbinding maken met de databaseservers.
- Stateful TDS pakket inspectie terwijl het verbindingen van clients accepteert. De gateway valideert de verbindingsgegevens en geeft de TDS-pakketten door aan de juiste fysieke server op basis van de databasenaam die is opgegeven in de verbindingstekenreeks.

Het overkoepelende principe voor netwerkbeveiliging van het Azure SQL Database-aanbod is om alleen de verbinding en communicatie toe te staan die nodig is om de service te laten werken. Alle andere poorten, protocollen en verbindingen zijn standaard geblokkeerd. Virtuele lokale netwerken (VLAN's) en ACL's worden gebruikt om netwerkcommunicatie te beperken via bron- en doelnetwerken, protocollen en poortnummers.

Mechanismen die zijn goedgekeurd voor het implementeren van netwerkgebaseerde ACL's, zijn acl.l.' Deze mechanismen worden beheerd door Azure-netwerken, host-firewall en Azure SQL Database-gatewayfirewallregels, die door de klant zijn geconfigureerd.

## <a name="data-segregation-and-customer-isolation"></a>Datasegregatie en klantisolatie
Het Azure-productienetwerk is zodanig gestructureerd dat openbaar toegankelijke systeemonderdelen worden gescheiden van interne resources. Er bestaan fysieke en logische grenzen tussen webservers die toegang bieden tot de openbare Azure-portal en de onderliggende Azure-virtuele infrastructuur, waar de exemplaren van klantgegevens en klantgegevens zich bevinden.

Alle openbaar toegankelijke informatie wordt beheerd binnen het Azure-productienetwerk. Het productienetwerk is onderworpen aan tweefactorauthenticatie- en grensbeveiligingsmechanismen, maakt gebruik van de firewall- en beveiligingsfunctieset die in de vorige sectie wordt beschreven en gebruikt functies voor gegevensisolatie zoals aangegeven in de volgende secties.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>Ongeautoriseerde systemen en isolatie van de FC
Omdat de fabriccontroller (FC) de centrale orchestrator van de Azure-stof is, zijn er aanzienlijke besturingselementen aanwezig om bedreigingen voor deze fabric te beperken, vooral van mogelijk gecompromitteerde FA's binnen klanttoepassingen. De FC herkent geen hardware waarvan de apparaatgegevens (bijvoorbeeld MAC-adres) niet vooraf binnen de FC zijn geladen. De DHCP-servers op de FC hebben lijsten geconfigureerd met MAC-adressen van de knooppunten die ze bereid zijn op te starten. Zelfs als ongeautoriseerde systemen zijn aangesloten, zijn ze niet opgenomen in de inventaris van de stof, en dus niet aangesloten of bevoegd om te communiceren met een systeem binnen de inventaris van de stof. Dit vermindert het risico dat ongeautoriseerde systemen communiceren met de FC en toegang krijgen tot de VLAN en Azure.

### <a name="vlan-isolation"></a>VLAN-isolatie
Het Azure-productienetwerk is logischerwijs gescheiden in drie primaire VLAN's:

- De belangrijkste VLAN: verbindt niet-vertrouwde klantknooppunten.
- De FC VLAN: Bevat vertrouwde FCs en ondersteunende systemen.
- Het apparaat VLAN: Bevat vertrouwd netwerk en andere infrastructuurapparaten.

### <a name="packet-filtering"></a>Pakketfiltering
Het IPFilter en de softwarefirewalls die zijn geïmplementeerd op het hoofdbesturingssysteem en het gastbesturingssysteem van de knooppunten, dwingen verbindingsbeperkingen af en voorkomen ongeautoriseerd verkeer tussen VM's.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hypervisor, root OS en gast VM's
De isolatie van het root-besturingssysteem van de gast-VM's en de gast-VM's van elkaar wordt beheerd door de hypervisor en het root-besturingssysteem.

### <a name="types-of-rules-on-firewalls"></a>Typen regels voor firewalls
Een regel wordt gedefinieerd als:

{Src IP, Src Port, Destination IP, Destination Port, Destination Protocol, In/Out, Stateful/Stateless, Stateful Flow Timeout}.

Syn-pakketten (Synchrone idle character) mogen alleen in of uit als een van de regels dit toestaat. Voor TCP gebruikt Azure staatloze regels waarbij het principe is dat het alleen alle niet-SYN-pakketten in of uit de VM toestaat. Het beveiligingsuitgangspunt is dat elke hoststack bestand is tegen het negeren van een niet-SYN als deze nog niet eerder een SYN-pakket heeft gezien. Het TCP-protocol zelf is stateful en in combinatie met de stateloze SYN-gebaseerde regel bereikt een algemeen gedrag van een stateful implementatie.

Voor UDP (User Datagram Protocol) gebruikt Azure een stateful rule. Elke keer dat een UDP-pakket overeenkomt met een regel, wordt een omgekeerde stroom in de andere richting gemaakt. Deze stroom heeft een ingebouwde time-out.

Klanten zijn verantwoordelijk voor het opzetten van hun eigen firewalls bovenop wat Azure biedt. Hier kunnen klanten de regels voor binnenkomend en uitgaand verkeer definiëren.

### <a name="production-configuration-management"></a>Beheer van productieconfiguratie
Standaardbeveiligde configuraties worden onderhouden door de respectievelijke bedrijfsteams in Azure en Azure SQL Database. Alle configuratiewijzigingen in productiesystemen worden gedocumenteerd en bijgehouden via een centraal volgsysteem. Software- en hardwarewijzigingen worden bijgehouden via het centrale volgsysteem. Netwerkwijzigingen die betrekking hebben op ACL worden bijgehouden met behulp van een ACL-beheerservice.

Alle configuratiewijzigingen in Azure worden ontwikkeld en getest in de faseringsomgeving en worden vervolgens geïmplementeerd in de productieomgeving. Software builds worden beoordeeld als onderdeel van het testen. Beveiligings- en privacycontroles worden beoordeeld als onderdeel van de criteria voor de invoerchecklist. Wijzigingen worden op geplande intervallen geïmplementeerd door het desbetreffende implementatieteam. Releases worden beoordeeld en ondertekend door het desbetreffende personeel van het implementatieteam voordat ze in productie worden genomen.

Veranderingen worden gecontroleerd op succes. Bij een foutscenario wordt de wijziging teruggezet naar de vorige status of wordt een hotfix geïmplementeerd om de fout met goedkeuring van het aangewezen personeel op te lossen. Source Depot, Git, TFS, Master Data Services (MDS), runners, Azure security monitoring, de FC en het WinFabric-platform worden gebruikt om de configuratie-instellingen in de virtuele Azure-omgeving centraal te beheren, toe te passen en te verifiëren.

Op dezelfde manier hebben hardware- en netwerkwijzigingen validatiestappen ingesteld om te beoordelen of ze voldoen aan de vereisten voor build. De releases worden beoordeeld en geautoriseerd via een gecoördineerde change advisory board (CAB) van de betreffende groepen in de stack.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over wat Microsoft doet om de Azure-infrastructuur te beveiligen:

- [Azure-faciliteiten, lokalen en fysieke beveiliging](physical-security.md)
- [Beschikbaarheid azure-infrastructuur](infrastructure-availability.md)
- [Onderdelen en grenzen van azure-informatiesysteem](infrastructure-components.md)
- [Azure-netwerkarchitectuur](infrastructure-network.md)
- [Azure-productienetwerk](production-network.md)
- [Azure-productiebewerkingen en -beheer](infrastructure-operations.md)
- [Azure-infrastructuurbewaking](infrastructure-monitoring.md)
- [Integriteit van Azure-infrastructuur](infrastructure-integrity.md)
- [Azure-klantgegevensbeveiliging](protection-customer-data.md)
