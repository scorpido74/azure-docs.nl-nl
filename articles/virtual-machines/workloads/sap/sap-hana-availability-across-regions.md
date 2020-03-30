---
title: BESCHIKBAARHEID VAN SAP HANA in Azure-regio's | Microsoft Documenten
description: Een overzicht van beschikbaarheidsoverwegingen bij het uitvoeren van SAP HANA op Azure VM's in meerdere Azure-regio's.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68a393865038722f2fd7fa5e42334f8d5e760951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70078856"
---
# <a name="sap-hana-availability-across-azure-regions"></a>BESCHIKBAARHEID VAN SAP HANA in Azure-regio's

In dit artikel worden scenario's beschreven met betrekking tot de beschikbaarheid van SAP HANA in verschillende Azure-regio's. Vanwege de afstand tussen Azure-regio's brengt het instellen van SAP HANA-beschikbaarheid in meerdere Azure-regio's speciale overwegingen met zich mee.

## <a name="why-deploy-across-multiple-azure-regions"></a>Waarom implementeren in meerdere Azure-regio's

Azure-regio's worden vaak gescheiden door grote afstanden. Afhankelijk van de geopolitieke regio kan de afstand tussen Azure-regio's honderden kilometers of zelfs enkele duizenden mijlen zijn, zoals in de Verenigde Staten. Vanwege de afstand heeft netwerkverkeer tussen assets die zijn geïmplementeerd in twee verschillende Azure-regio's een aanzienlijke latentie van netwerkretourvluchten. De latentie is significant genoeg om synchrone gegevensuitwisseling tussen twee SAP HANA-exemplaren uit te sluiten onder typische SAP-workloads. 

Aan de andere kant hebben organisaties vaak een afstandsvereiste tussen de locatie van het primaire datacenter en een secundair datacenter. Een vereiste op afstand helpt bij het bieden van beschikbaarheid als zich een natuurramp voordoet op een bredere geografische locatie. Voorbeelden hiervan zijn de orkanen die het Caribisch gebied en Florida in september en oktober 2017 hebben getroffen. Uw organisatie heeft mogelijk ten minste een minimale afstandsvereiste. Voor de meeste Azure-klanten vereist een definitie van minimale afstand u voor het ontwerpen voor beschikbaarheid in [Azure-regio's.](https://azure.microsoft.com/regions/) Omdat de afstand tussen twee Azure-regio's te groot is om de HANA synchrone replicatiemodus te gebruiken, kunnen RTO- en RPO-vereisten u dwingen beschikbaarheidsconfiguraties in één regio te implementeren en vervolgens in een seconde aanvullen met extra implementaties Regio.

Een ander aspect te overwegen in dit scenario is failover en client redirect. De aanname is dat een failover tussen SAP HANA-exemplaren in twee verschillende Azure-regio's altijd een handmatige failover is. Omdat de replicatiemodus van SAP HANA-systeemreplicatie is ingesteld op asynchrone, is er een potentieel dat gegevens die zijn vastgelegd in het primaire HANA-exemplaar nog niet zijn doorgegaan naar het secundaire HANA-exemplaar. Automatische failover is daarom geen optie voor configuraties waarbij de replicatie asynchroon is. Zelfs met handmatig gecontroleerde failover, zoals bij een failover-oefening, moet u maatregelen nemen om ervoor te zorgen dat alle vastgelegde gegevens aan de primaire kant de secundaire instantie hebben gehaald voordat u handmatig naar de andere Azure-regio gaat.
 
Azure Virtual Network maakt gebruik van een ander IP-adresbereik. De IP-adressen worden geïmplementeerd in de tweede Azure-regio. U moet dus de SAP HANA-clientconfiguratie wijzigen, of bij voorkeur moet u stappen maken om de naamomzetting te wijzigen. Op deze manier worden de clients doorgestuurd naar het IP-adres van de nieuwe secundaire site. Zie voor meer informatie het SAP-artikel [Client connection recovery after takeover](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Eenvoudige beschikbaarheid tussen twee Azure-regio's

U ervoor kiezen om geen beschikbaarheidsconfiguratie in één regio in te voeren, maar toch de vraag te hebben om de werkbelasting te laten uitvoeren als zich een ramp voordoet. Typische gevallen voor dergelijke scenario's zijn niet-productiesystemen. Hoewel het hebben van het systeem uit voor een halve dag of zelfs een dag is duurzaam, u niet toestaan dat het systeem niet beschikbaar zijn voor 48 uur of meer. Voer een ander systeem uit dat nog minder belangrijk is in de VM om de installatie goedkoper te maken. Het andere systeem functioneert als bestemming. U de vm ook in het secundaire gebied kleiner maken en ervoor kiezen de gegevens niet vooraf te laden. Omdat de failover handmatig is en veel meer stappen met zich meebrengt om te mislukken over de volledige toepassingsstack, is de extra tijd om de VM af te sluiten, het formaat ervan te wijzigen en vervolgens de VM opnieuw op te starten acceptabel.

Als u het scenario gebruikt om het DR-doel te delen met een QA-systeem in één VM, moet u rekening houden met deze overwegingen:

- Er zijn twee [bewerkingsmodi](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) met delta_datashipping en logreplay, die beschikbaar zijn voor een dergelijk scenario
- Beide bewerkingsmodi hebben verschillende geheugenvereisten zonder vooraf ladende gegevens
- Delta_datashipping kan drastisch minder geheugen vereisen zonder de preload-optie dan logreplay zou kunnen vereisen. Zie hoofdstuk 4.3 van het SAP-document [Hoe systeemreplicatie voor SAP HANA uit te voeren](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf)
- De geheugenvereiste van de logreplay-bewerkingsmodus zonder voorbelasting is niet deterministisch en is afhankelijk van de geladen structuren in het kolomarchief. In extreme gevallen hebt u mogelijk 50% van het geheugen van de primaire instantie nodig. Het geheugen voor de logreplay-bewerkingsmodus is onafhankelijk van de vraag of u ervoor hebt gekozen om de vooraf geïnstalleerde gegevens al dan niet in te stellen.


![Diagram van twee VM's over twee regio's](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> In deze configuratie u geen RPO=0 leveren omdat de HANA-systeemreplicatiemodus asynchroon is. Als u een RPO=0 moet leveren, is deze configuratie niet de configuratie naar keuze.

Een kleine wijziging die u in de configuratie aanbrengen, is mogelijk om gegevens als preloading te configureren. Echter, gezien de handmatige aard van failover en het feit dat de toepassing lagen ook moeten verhuizen naar het tweede gebied, is het misschien niet zinvol om vooraf laden van gegevens. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Beschikbaarheid binnen één regio en tussen regio's combineren 

Een combinatie van beschikbaarheid binnen en tussen regio's kan worden gedreven door deze factoren:

- Een vereiste van RPO=0 binnen een Azure-gebied.
- De organisatie is niet bereid of in staat om wereldwijde operaties te laten beïnvloeden door een grote natuurramp die een groter gebied treft. Dit was het geval voor sommige orkanen die het Caribisch gebied getroffen in de afgelopen jaren.
- Regelgeving die afstanden vereist tussen primaire en secundaire sites die duidelijk verder gaan dan wat Azure-beschikbaarheidszones kunnen bieden.

In deze gevallen u instellen wat SAP een [SAP HANA multitier systeemreplicatieconfiguratie](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) noemt met HANA-systeemreplicatie. De architectuur zou eruit zien als:

![Diagram van drie VM's over twee regio's](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

SAP introduceerde [multi-target systeemreplicatie](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html) met HANA 2.0 SPS3. Multi-target systeemreplicatie brengt enkele voordelen met zich mee in updatescenario's. De DR-site (regio 2) wordt bijvoorbeeld niet beïnvloed wanneer de secundaire HA-site is uitgeschakeld voor onderhoud of updates. U meer informatie vinden over HANA multi-target systeemreplicatie [hier.](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html)
Mogelijke architectuur met multi-target replicatie zou eruit zien als:

![Diagram van drie VM's over twee regio's milti-target](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

Als de organisatie vereisten heeft voor gereedheid met hoge beschikbaarheid in de tweede(DR) Azure-regio, ziet de architectuur eruit als volgt:

![Diagram van drie VM's over twee regio's milti-target](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


Met behulp van logreplay als bewerkingsmodus biedt deze configuratie een RPO=0, met lage RTO, binnen het primaire gebied. De configuratie biedt ook fatsoenlijke RPO als een verhuizing naar de tweede regio is betrokken. De RTO-tijden in het tweede gebied zijn afhankelijk van de vraag of gegevens vooraf zijn geladen. Veel klanten gebruiken de VM in de secundaire regio om een testsysteem uit te voeren. In dat gebruiksgeval kunnen de gegevens niet vooraf worden geladen.

> [!IMPORTANT]
> De werkingsmodi tussen de verschillende lagen moeten homogeen zijn. U logreply **niet** gebruiken als bewerkingsmodus tussen laag 1 en niveau 2 en delta_datashipping om laag 3 te leveren. U alleen de ene of de andere bewerkingsmodus kiezen die consistent moet zijn voor alle lagen. Aangezien delta_datashipping niet geschikt is om u een RPO=0 te geven, blijft de enige redelijke bewerkingsmodus voor een dergelijke multi-tier configuratie logreplay. Zie de [werkingsmodi voor SAP HANA-systeemreplicatie voor](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html)meer informatie over de bewerkingsmodi en enkele beperkingen. 

## <a name="next-steps"></a>Volgende stappen

Zie voor stapsgewijze richtlijnen voor het instellen van deze configuraties in Azure:

- [SAP HANA-systeemreplicatie instellen in Azure VM's](sap-hana-high-availability.md)
- [Hoge beschikbaarheid voor SAP HANA door gebruik te maken van systeemreplicatie](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
