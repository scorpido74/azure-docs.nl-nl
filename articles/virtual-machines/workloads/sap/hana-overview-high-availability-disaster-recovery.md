---
title: Hoge beschikbaarheid en noodherstel van SAP HANA op Azure (grote exemplaren) | Microsoft Documenten
description: Hoge beschikbaarheid vaststellen en plannen voor noodherstel van SAP HANA op Azure (Grote exemplaren)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0585c1251ba18e1390f3eee28a989edee6eb8591
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616930"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>SAP HANA Large Instances hoge beschikbaarheid en disaster recovery op Azure 

>[!IMPORTANT]
>Deze documentatie is geen vervanging van de SAP HANA administratie documentatie of SAP Notes. De verwachting is dat de lezer een goed begrip en expertise in SAP HANA administratie en operaties heeft, vooral met de onderwerpen van back-up, herstel, hoge beschikbaarheid, en disaster recovery.

Het is belangrijk dat u stappen en processen uitoefent die in uw omgeving en met uw HANA-versies en -releases worden genomen. Sommige processen die in deze documentatie worden beschreven, worden vereenvoudigd voor een beter algemeen begrip en zijn niet bedoeld om te worden gebruikt als gedetailleerde stappen voor uiteindelijke handboeken voor de werking. Als u bewerkingshandboeken voor uw configuraties wilt maken, moet u uw processen testen en uitvoeren en de processen documenteren die verband houden met uw specifieke configuraties. 


Hoge beschikbaarheid en disaster recovery (DR) zijn cruciale aspecten van het uitvoeren van uw bedrijfskritische SAP HANA op de Azure (Large Instances) server. Het is belangrijk om samen te werken met SAP, uw systeemintegrator of Microsoft om de juiste strategieën voor hoge beschikbaarheid en noodherstel goed te ontwerpen en te implementeren. Het is ook belangrijk om de doelstelling van het herstelpunt (RPO) en de doelstelling voor hersteltijd te overwegen, die specifiek zijn voor uw omgeving.

Microsoft ondersteunt sommige SAP HANA-mogelijkheden met hoge beschikbaarheid met HANA Large Instances. Deze mogelijkheden omvatten:

- **Opslagreplicatie:** de mogelijkheid van het opslagsysteem om alle gegevens te repliceren naar een andere HANA-stempel voor grote instanties in een ander Azure-gebied. SAP HANA werkt onafhankelijk van deze methode. Deze functionaliteit is het standaard mechanisme voor noodherstel dat wordt aangeboden voor HANA Large Instances.
- **HANA-systeemreplicatie**: De [replicatie van alle gegevens in SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) naar een afzonderlijk SAP HANA-systeem. De doelstelling hersteltijd wordt met regelmatige tussenpozen geminimaliseerd door middel van gegevensreplicatie. SAP HANA ondersteunt asynchrone, synchrone in-memory en synchrone modi. De synchrone modus wordt alleen gebruikt voor SAP HANA-systemen die zich binnen hetzelfde datacenter bevinden of minder dan 100 km uit elkaar liggen. Met het huidige ontwerp van HANA Large Instance-stempels kan HANA-systeemreplicatie alleen worden gebruikt voor hoge beschikbaarheid binnen één regio. Hana-systeemreplicatie vereist een reverse proxy- of routeringscomponent van derden voor noodherstelconfiguraties in een andere Azure-regio. 
- **Host auto-failover:** een lokale oplossing voor het herstellen van fouten voor SAP HANA die een alternatief is voor HANA-systeemreplicatie. Als het hoofdknooppunt niet meer beschikbaar is, configureert u een of meer stand-by SAP HANA-knooppunten in de schaalmodus en mislukt SAP HANA automatisch naar een stand-by-knooppunt.

SAP HANA op Azure (Large Instances) wordt aangeboden in twee Azure-regio's in vier geopolitieke gebieden (VS, Australië, Europa en Japan). Twee regio's binnen een geopolitiek gebied waar HANA Large Instance-postzegels worden ontvangen, zijn verbonden met afzonderlijke speciale netwerkcircuits. Deze worden gebruikt voor het repliceren van opslagmomentopnamen om methoden voor noodherstel te bieden. De replicatie is niet standaard ingesteld, maar is ingesteld voor klanten die een noodherstelfunctionaliteit bestellen. Opslagreplicatie is afhankelijk van het gebruik van opslagmomentopnamen voor HANA Large Instances. Het is niet mogelijk om een Azure-regio te kiezen als een DR-regio die zich in een ander geopolitiek gebied bevindt. 

In de volgende tabel worden de momenteel ondersteunde methoden en combinaties voor herstel van rampen met hoge beschikbaarheid en rampen weergegeven:

| Scenario ondersteund in HANA Large Instances | Optie voor hoge beschikbaarheid | Optie voor noodherstel | Opmerkingen |
| --- | --- | --- | --- |
| Eén knooppunt | Niet beschikbaar. | Speciale DR setup.<br /> Multifunctionele DR-installatie. | |
| Host auto-failover: Scale-out (met of zonder stand-by)<br /> inclusief 1+1 | Mogelijk met de stand-by die de actieve rol op zich neemt.<br /> HANA regelt de rolschakelaar. | Speciale DR setup.<br /> Multifunctionele DR-installatie.<br /> DR-synchronisatie met behulp van opslagreplicatie. | HANA-volumesets zijn gekoppeld aan alle knooppunten.<br /> DR-site moet hetzelfde aantal knooppunten hebben. |
| HANA-systeemreplicatie | Mogelijk met primaire of secundaire setup.<br /> Secundaire wordt verplaatst naar primaire rol in een failovercase.<br /> HANA-systeemreplicatie en OS-controlefailover. | Speciale DR setup.<br /> Multifunctionele DR-installatie.<br /> DR-synchronisatie met behulp van opslagreplicatie.<br /> DR met behulp van HANA-systeemreplicatie is nog niet mogelijk zonder onderdelen van derden. | Aan elk knooppunt zijn afzonderlijke set schijfvolumes gekoppeld.<br /> Alleen schijfvolumes van secundaire replica's in de productiesite worden gerepliceerd naar de DR-locatie.<br /> Eén set volumes is vereist op de DR-site. | 

Een speciale DR-installatie is waar de HANA Large Instance-eenheid op de DR-site niet wordt gebruikt voor het uitvoeren van een andere werkbelasting of een niet-productiesysteem. Het apparaat is passief en wordt alleen ingezet als een mislukte ramp wordt uitgevoerd. Hoewel, deze setup is niet een voorkeur keuze voor veel klanten.

Doorverwijzen [HLI-ondersteunde scenario's](hana-supported-scenario.md) voor meer informatie over de indeling van opslag en ethernetdetails voor uw architectuur.

> [!NOTE]
> [SAP HANA MCOD-implementaties](https://launchpad.support.sap.com/#/notes/1681092) (meerdere HANA-exemplaren op één eenheid) werken als overlagenscenario's met de HA- en DR-methoden in de tabel. Een uitzondering is het gebruik van HANA-systeemreplicatie met een automatisch failovercluster op basis van Pacemaker. Een dergelijk geval ondersteunt slechts één HANA-exemplaar per eenheid. Voor [SAP HANA MDC-implementaties](https://launchpad.support.sap.com/#/notes/2096000) werken alleen ha- en DR-methoden op basis van opslag als er meer dan één tenant wordt geïmplementeerd. Als één tenant is geïmplementeerd, zijn alle vermelde methoden geldig.  

Een multifunctionele DR-installatie is waar de HANA Large Instance-eenheid op de DR-site een niet-productiewerkbelasting uitvoert. In geval van een ramp sluit u het niet-productiesysteem af, monteert u de opslaggerepliceerde (extra) volumesets en start u vervolgens de productie-HANA-instantie. De meeste klanten die gebruik maken van de HANA Large Instance disaster recovery-functionaliteit gebruiken deze configuratie. 


Meer informatie over SAP HANA hoge beschikbaarheid vindt u in de volgende SAP-artikelen: 

- [WHITEPAPER over hoge beschikbaarheid van SAP HANA](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA-beheergids](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA Academy Video over SAP HANA System Replication](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP Support Note #1999880 – Veelgestelde vragen over SAP HANA-systeemreplicatie](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [SAP Support Note #2165547 – SAP HANA Back-up en herstel binnen SAP HANA-systeemreplicatieomgeving](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP Support Note #1984882 – SAP HANA-systeemreplicatie gebruiken voor hardware-uitwisseling met minimale/nul downtime](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Netwerkoverwegingen voor noodherstel met HANA Large Instances

Als u wilt profiteren van de functionaliteit voor noodherstel van HANA Large Instances, moet u netwerkconnectiviteit met de twee Azure-regio's ontwerpen. U hebt een Azure ExpressRoute-circuitverbinding nodig van on-premises in uw belangrijkste Azure-regio en een andere circuitverbinding van on-premises naar uw regio voor noodherstel. Deze maatregel heeft betrekking op een situatie waarin er een probleem is in een Azure-regio, waaronder een MSEE-locatie (Microsoft Enterprise Edge Router).

Als tweede meting u alle virtuele Azure-netwerken die verbinding maken met SAP HANA op Azure (Large Instances) in de ene regio verbinden met een ExpressRoute-circuit dat HANA Large Instances in de andere regio verbindt. Met deze *cross connect*kunnen services die worden uitgevoerd op een Virtueel Azure-netwerk in regio 1 verbinding maken met HANA Large Instance-eenheden in regio 2 en andersom. Met deze maatregel wordt een geval behandeld waarin slechts één van de MSEE-locaties die verbinding maakt met uw on-premises locatie met Azure offline gaat.

De volgende afbeelding illustreert een veerkrachtige configuratie voor gevallen van noodherstel:

![Optimale configuratie voor noodherstel](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Andere vereisten met HANA Large Instances-opslagreplicatie voor herstel na noodgevallen

Naast de voorgaande vereisten voor een noodherstel-installatie met HANA Large Instances, moet u:

- Bestel SAP HANA op Azure (Large Instances) SKU's van dezelfde grootte als uw productie-SKU's en implementeer ze in het gebied voor noodherstel. In de huidige klantimplementaties worden deze exemplaren gebruikt om niet-productieHANA-exemplaren uit te voeren. Deze configuraties worden aangeduid als *multifunctionele DR-opstellingen.*   
- Bestel extra opslag op de DR-site voor elk van uw SAP HANA op Azure (Large Instances) SKU's die u wilt herstellen op de site voor noodherstel. Als u extra opslagruimte koopt, u de opslagvolumes toewijzen. U de volumes die het doel zijn van de opslagreplicatie van uw productie-Azure-regio toewijzen aan het Azure-gebied voor noodherstel.
- In het geval dat u HSR-instellingen op primaire en op opslag gebaseerde replicatie op de DR-site hebt ingesteld, extra opslag op de DR-site moet aanschaffen, zodat zowel primaire als secundaire knooppuntengegevens worden gerepliceerd naar de DR-site.

  **Volgende stappen**
- [Back-up en herstel doorverwijzen](hana-backup-restore.md).













