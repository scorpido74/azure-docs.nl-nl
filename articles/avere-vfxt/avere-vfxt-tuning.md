---
title: Avere vFXT-clustertuning - Azure
description: Overzicht van aangepaste instellingen om de prestaties in Avere vFXT voor Azure te optimaliseren
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: df20f050ff87fdb59a3e5cca373098240f8bfbb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76152932"
---
# <a name="cluster-tuning"></a>Cluster afstemmen

De meeste vFXT-clusters kunnen profiteren van aangepaste prestatie-instellingen. Met deze instellingen kan het cluster het beste werken met uw specifieke werkstroom, gegevensset en hulpprogramma's.

Deze aanpassing moet worden gedaan met de hulp van een ondersteuningsmedewerker, omdat het mogelijk is om functies te configureren die niet beschikbaar zijn in het Configuratiescherm van Avere.

In deze sectie wordt een aantal van de aangepaste tuning beschreven die kan worden gedaan.

## <a name="general-optimizations"></a>Algemene optimalisaties

Deze wijzigingen kunnen worden aanbevolen op basis van gegevenssetkwaliteiten of werkstroomstijl.

* Als de werkbelasting schrijfzwaar is, verhoogt u de grootte van de schrijfcache ten opzichte van de standaardwaarde van 20%.
* Als de gegevensset veel kleine bestanden omvat, verhoogt u de limiet voor het aantal bestanden van de clustercache.
* Als het werk bestaat uit het kopiëren of verplaatsen van gegevens tussen twee opslagplaatsen, past u het aantal threads aan dat wordt gebruikt voor het verplaatsen van gegevens:
  * Om de snelheid te verhogen, u het aantal gebruikte parallelle threads verhogen.
  * Als het back-endopslagvolume overbelast raakt, moet u mogelijk het aantal gebruikte parallelle threads verminderen.
* Als het cluster gegevens in caches voor een core filer die NFSv4 ACL's gebruikt, schakelt u de cachemodus in om de bestandsautorisatie voor bepaalde clients te stroomlijnen.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Cloud NAS- of cloudgateway-optimalisaties

In een cloud-NAS- of gatewayscenario biedt het vFXT-cluster NAS-stijl toegang tot een cloudcontainer. Als u wilt profiteren van hogere gegevenssnelheden tussen het vFXT-cluster en cloudopslag, kan uw vertegenwoordiger aanbevelen instellingen te wijzigen om gegevens agressiever naar het opslagvolume vanuit de cache te pushen. Bijvoorbeeld:

* Het aantal TCP-verbindingen tussen het cluster en de opslagcontainer verhogen

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Cloud bursting of hybride WAN optimalisaties

In een scenario voor het uitbarsten van de cloud of het WAN-optimalisatiescenario voor hybride opslag biedt het vFXT-cluster integratie tussen de cloud en on-premises hardwareopslag. Deze wijzigingen kunnen nuttig zijn:

* Het aantal toegestane TCP-verbindingen tussen het cluster en de kernfiler verhogen
* Schakel de WAN-optimalisatieinstelling in voor de externe kernfiler (Deze instelling kan worden gebruikt voor een externe on-premises filer of een cloudcore-filer in een andere Azure-regio.)
* De grootte van de TCP-socketbuffer vergroten<sup>*</sup>
* De instelling 'altijd vooruit' inschakelen om bestanden met redundante cache te verminderen<sup>*</sup>

<sup>*</sup>Deze aanpassingen zijn mogelijk niet voor alle systemen van toepassing, afhankelijk van de werkbelasting en prestatiebehoeften.

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Help uw Avere vFXT voor Azure te optimaliseren

Als u contact wilt opnemen met ondersteuningsmedewerkers over deze optimalisaties, gebruikt u de procedure die wordt beschreven in [Hulp bij uw systeem zoeken.](avere-vfxt-open-ticket.md)
