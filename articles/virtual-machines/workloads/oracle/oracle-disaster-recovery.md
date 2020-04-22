---
title: Overzicht van een Oracle-scenario voor noodherstel in uw Azure-omgeving | Microsoft Documenten
description: Een scenario voor noodherstel voor een Oracle Database 12c-database in uw Azure-omgeving
services: virtual-machines-linux
documentationcenter: virtual-machines
author: BorisB2015
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: borisb
ms.openlocfilehash: adf4c54d4f43f806ae985f7c1523303cf574b4c1
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687470"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Disaster recovery voor een Oracle Database 12c-database in een Azure-omgeving

## <a name="assumptions"></a>Veronderstellingen

- U hebt inzicht in het ontwerp van Oracle Data Guard en Azure-omgevingen.


## <a name="goals"></a>Doelstellingen
- Ontwerp de topologie en configuratie die voldoen aan uw DR-vereisten (disaster recovery).

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Scenario 1: Primaire en DR-sites op Azure

Een klant heeft een Oracle-database ingesteld op de primaire site. Een DR-site bevindt zich in een andere regio. De klant gebruikt Oracle Data Guard voor snel herstel tussen deze sites. De primaire site heeft ook een secundaire database voor rapportage en andere toepassingen. 

### <a name="topology"></a>Topologie

Hier vindt u een overzicht van de Azure-installatie:

- Twee sites (een primaire site en een DR-site)
- Twee virtuele netwerken
- Twee Oracle-databases met Data Guard (primair en stand-by)
- Twee Oracle-databases met Golden Gate of Data Guard (alleen primaire site)
- Twee toepassingsservices, één primair en één op de DR-site
- Een *beschikbaarheidsset,* die wordt gebruikt voor database- en toepassingsservice op de primaire site
- Eén jumpbox op elke site, die de toegang tot het privénetwerk beperkt en alleen aanmelding door een beheerder toestaat
- Een jumpbox, applicatieservice, database en VPN-gateway op afzonderlijke subnetten
- NSG afgedwongen op applicatie- en database-subnetten

![Schermafbeelding van de pagina DR-topologie](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Scenario 2: On-premises primaire site en DR-site op Azure

Een klant heeft een on-premises Oracle-database-installatie (primaire site). Een DR-site bevindt zich op Azure. Oracle Data Guard wordt gebruikt voor snel herstel tussen deze sites. De primaire site heeft ook een secundaire database voor rapportage en andere toepassingen. 

Er zijn twee benaderingen voor deze setup.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Aanpak 1: Directe verbindingen tussen on-premises en Azure, waarvoor open TCP-poorten op de firewall nodig zijn 

We raden geen directe verbindingen aan omdat ze de TCP-poorten blootstellen aan de buitenwereld.

#### <a name="topology"></a>Topologie

Hieronder volgt een overzicht van de Azure-installatie:

- Eén DR-site 
- Eén virtueel netwerk
- Eén Oracle-database met Data Guard (actief)
- Eén toepassingsservice op de DR-site
- Eén jumpbox, die de toegang tot het privénetwerk beperkt en alleen aanmelding door een beheerder toestaat
- Een jumpbox, applicatieservice, database en VPN-gateway op afzonderlijke subnetten
- NSG afgedwongen op applicatie- en database-subnetten
- Een NSG-beleid/regel om binnenkomende TCP-poort 1521 (of een door de gebruiker gedefinieerde poort) toe te staan
- Een NSG-beleid/regel om alleen het IP-adres/de on-premises (DB of toepassing) te beperken tot toegang tot het virtuele netwerk

![Schermafbeelding van de pagina DR-topologie](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Aanpak 2: Site-to-site VPN
Site-to-site VPN is een betere aanpak. Zie Een virtueel netwerk maken [met een Site-to-Site VPN-verbinding met CLI](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)voor meer informatie over het instellen van een VPN.

#### <a name="topology"></a>Topologie

Hieronder volgt een overzicht van de Azure-installatie:

- Eén DR-site 
- Eén virtueel netwerk 
- Eén Oracle-database met Data Guard (actief)
- Eén toepassingsservice op de DR-site
- Eén jumpbox, die de toegang tot het privénetwerk beperkt en alleen aanmelding door een beheerder toestaat
- Een jumpbox, applicatieservice, database en VPN-gateway staan op afzonderlijke subnetten
- NSG afgedwongen op applicatie- en database-subnetten
- Vpn-verbinding van site naar site tussen on-premises en Azure

![Schermafbeelding van de pagina DR-topologie](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Aanvullende lezing

- [Een Oracle-database ontwerpen en implementeren op Azure](oracle-design.md)
- [Oracle Data Guard configureren](configure-oracle-dataguard.md)
- [Oracle Golden Gate configureren](configure-oracle-golden-gate.md)
- [Oracle back-up en herstel](oracle-backup-recovery.md)


## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: zeer beschikbare VM's maken](../../linux/create-cli-complete.md)
- [Azure CLI-voorbeelden voor VM-implementatie verkennen](../../linux/cli-samples.md)
