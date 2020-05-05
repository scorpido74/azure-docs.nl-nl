---
title: 'Zelf studie: netwerk controlelijst'
description: Vereisten voor netwerk vereisten en Details over netwerk connectiviteit en netwerk poorten
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 42bd579a455c7efe3b8f6e4d4154e687726adb1d
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740148"
---
# <a name="networking-checklist-for-azure-vmware-solution-avs"></a>Controle lijst voor netwerken voor Azure VMware-oplossing (AVS)

De Azure VMware-oplossing biedt een VMware-privécloud, die toegankelijk is voor gebruikers en toepassingen van on-premises en Azure-omgevingen of-resources. De connectiviteit wordt geleverd via netwerk services, zoals Azure ExpressRoute en VPN-verbindingen, en vereist een aantal specifieke netwerkadresbereiken en Firewall poorten voor het inschakelen van de services. In dit artikel vindt u de informatie die u nodig hebt om uw netwerk correct te configureren voor gebruik met AVS.

In deze zelf studie leert u het volgende:

> [!div class="checklist"]
> * Vereisten voor netwerk verbinding
> * DHCP in AVS

## <a name="network-connectivity"></a>Netwerkconnectiviteit

De AVS-privécloud is verbonden met uw virtuele Azure-netwerk met behulp van een Azure ExpressRoute-verbinding. Met deze verbinding met hoge band breedte en lage latentie kunt u toegang krijgen tot services die worden uitgevoerd in uw Azure-abonnement vanuit uw privécloud.

Voor persoonlijke Clouds van AVS is mini `/22` maal een CIDR-netwerk adres blok vereist voor subnetten, zoals hieronder wordt weer gegeven. Dit netwerk vormt een aanvulling op uw on-premises netwerken. Als u verbinding wilt maken met on-premises omgevingen en virtuele netwerken, moet dit een niet-overlappend netwerk adres blok zijn.

Voor `/22` beeld van een CIDR-netwerk adres blok:`10.10.0.0/22`

De subnetten:

| Netwerkgebruik             | Subnet | Voorbeeld        |
| ------------------------- | ------ | -------------- |
| Privécloudbeheer            | `/24`    | `10.10.0.0/24`   |
| vMotion-netwerk       | `/24`    | `10.10.1.0/24`   |
| VM-workloads | `/24`   | `10.10.2.0/24`   |
| ExpressRoute-peering | `/24`    | `10.10.3.8/30`   |

### <a name="network-ports-required-to-communicate-with-the-service"></a>Netwerk poorten die nodig zijn om te communiceren met de service

Bron|Doel|Protocol |Poort |Beschrijving  |Opmerking
---|-----|:-----:|:-----:|-----|-----
DNS-server voor de privécloud  |On-premises DNS-server  |UDP |53|DNS-client-aanvragen door sturen van PC vCenter voor alle on-premises DNS-query's (Zie de sectie DNS hieronder) |
On-premises DNS-server  |DNS-server voor de privécloud  |UDP |53|DNS-client-aanvragen voor het door sturen van on-premises Services naar particuliere cloud DNS servers (Zie DNS-sectie hieronder)
On-premises netwerk  |VCenter-Server voor privécloud  |TCP (HTTP)  |80|voor vCenter Server is poort 80 vereist voor directe HTTP-verbindingen.Poort 80 stuurt aanvragen om naar HTTPS-poort 443. Met deze omleiding kunt u in plaats `http://server` van `https://server`gebruiken.  <br><br>Voor WS-Management (hiervoor moet poort 443 zijn geopend) <br><br>Als u een aangepaste micro soft-SQL database gebruikt en niet de gebundelde SQL Server 2008-Data Base op de vCenter Server, wordt poort 80 door de SQL Reporting-Services gebruikt. Wanneer u vCenter Server installeert, wordt u door het installatie programma gevraagd de HTTP-poort voor de vCenter Server te wijzigen. Wijzig de vCenter Server HTTP-poort in een aangepaste waarde om te zorgen voor een geslaagde installatie.Micro soft Internet Information Services (IIS) maakt ook gebruik van poort 80. Zie conflict tussen vCenter Server en IIS voor poort 80.
Particulier Cloud beheer netwerk |On-premises Active Directory  |TCP  |389|Deze poort moet geopend zijn op de lokale en alle externe exemplaren van vCenter Server. Deze poort is het LDAP-poort nummer voor de Directory Services voor de vCenter Server groep. Het vCenter Server-systeem moet binden aan poort 389, zelfs als u dit vCenter Server exemplaar niet koppelt aan een gekoppelde modus groep. Als er een andere service wordt uitgevoerd op deze poort, is het misschien beter om deze te verwijderen of om de poort te wijzigen in een andere poort. U kunt de LDAP-service uitvoeren op elke poort van 1025 tot en met 65535.Als deze instantie als micro soft Windows Active Directory fungeert, wijzigt u het poort nummer van 389 in een beschik bare poort van 1025 tot en met 65535. Deze poort is optioneel: voor het configureren van on-premises AD als een identiteits bron op de Privécloud
On-premises netwerk  |VCenter-Server voor privécloud  |TCP (HTTPS)  |443|Met deze poort krijgt u toegang tot vCenter vanuit een on-premises netwerk.De standaard poort die het vCenter Server systeem gebruikt om te Luis teren naar verbindingen van de vSphere-client. Open poort 443 in de firewall om het vCenter Server systeem in staat te stellen gegevens van de vSphere-client te ontvangen. Het vCenter Server-systeem gebruikt poort 443 ook voor het bewaken van gegevens overdracht van SDK-clients.Deze poort wordt ook gebruikt voor de volgende services: WS-Management (hiervoor moet ook poort 80 zijn geopend). vSphere-client toegang tot vSphere-update beheer. Client verbindingen van het netwerkbeheer van derden om te vCenter Server. Netwerkbeheer clients van derden hebben toegang tot hosts. 
Webbrowser  | Hybride Cloud Manager  | TCP (https) | 9443 | De beheer interface van de virtuele machine van het hybride Cloud beheer voor de hybride Cloud Manager-systeem configuratie.
Beheer netwerk  | Hybride Cloud Manager |SSH |22| SSH-beheerders toegang tot hybride Cloud beheer.
HCM |   Cloud gateway|TCP (HTTPS) |8123| Verzend instructies voor de replicatie service op basis van de host naar de hybride Cloud gateway.
HCM |   Cloud gateway  |    HTTP TCP (HTTPS) |    9443  |  Beheer instructies verzenden naar de lokale hybride Cloud gateway met behulp van de REST API.
Cloud gateway|      L2C |    TCP (HTTP)  |   443 |   Beheer instructies verzenden van de Cloud gateway naar L2C wanneer L2C hetzelfde pad gebruikt als de hybride Cloud gateway.
Cloud gateway |     ESXi-hosts |     TCP |    80.902  |   Beheer-en OVF-implementatie
Cloud gateway (lokaal)|     Cloud gateway (extern)|     UDP  |   4500 | Vereist voor IPSEC<br>   Internet Key Exchange (IKEv2) voor het inkapselen van werk belastingen voor de bidirectionele tunnel. Netwerkadresomzetting-Traversal (NAT-T) wordt ook ondersteund.
Cloud gateway (lokaal)  |   Cloud gateway (extern)  |   UDP  |   500   | Vereist voor IPSEC<br> ISAKMP (Internet Key Exchange) voor de bidirectionele tunnel.
On-premises vCenter-netwerk|      Particulier Cloud beheer netwerk|      TCP  |    8000    |  vMotion van Vm's van on-premises vCenter naar Privécloud-vCenter   |     

## <a name="dhcp"></a>DHCP

Toepassingen en werk belastingen die worden uitgevoerd in een privécloud-omgeving, moeten naam omzetting en DHCP-services voor lookup-en IP-adres toewijzing hebben. U hebt de juiste DHCP-en DNS-infra structuur nodig om deze services te kunnen leveren. U kunt een virtuele machine configureren om deze services te leveren in uw privécloud.  
Het is raadzaam om de DHCP-service te gebruiken die is ingebouwd in NSX of met behulp van een lokale DHCP-server in de privécloud in plaats van het broadcasten van DHCP-verkeer via het WAN terug naar on-premises.

In deze zelf studie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Vereisten voor netwerk verbinding
> * DHCP in AVS

## <a name="next-steps"></a>Volgende stappen

Zodra u de juiste netwerk functionaliteit hebt, gaat u verder met de volgende zelf studie om uw AVS-privécloud te maken.

> [!div class="nextstepaction"]
> [Zelf studie: een privécloud maken](tutorial-create-private-cloud.md)
