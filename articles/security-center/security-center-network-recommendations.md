---
title: Uw netwerk bronnen beveiligen in Azure Security Center
description: Dit document bevat aanbevelingen in Azure Security Center waarmee u uw Azure-netwerk bronnen kunt beveiligen en blijven voldoen aan het beveiligings beleid.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: memildin
ms.openlocfilehash: 792b95b120f67afcd360730acbd783a3071388b2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77431482"
---
# <a name="protect-your-network-resources"></a>Uw netwerk bronnen beveiligen
Azure Security Center de beveiligings status van uw Azure-resources voortdurend geanalyseerd op de aanbevolen procedures voor netwerk beveiliging. Wanneer Security Center mogelijke beveiligings problemen identificeert, worden er aanbevelingen gemaakt die u door het proces van het configureren van de benodigde besturings elementen leiden om uw resources te beschermen en te beveiligen.

In dit artikel wordt de pagina **netwerken** van de sectie resource beveiliging van Security Center beschreven.

Zie [aanbevelingen voor netwerken](recommendations-reference.md#recs-network)voor een volledige lijst met aanbevelingen voor netwerken.

In dit artikel worden aanbevelingen gedaan die van toepassing zijn op uw Azure-resources op basis van een netwerk beveiligings perspectief. Aanbevelingen voor netwerken rond de volgende generatie firewalls, netwerk beveiligings groepen, JIT-VM-toegang, meer regels voor binnenkomend verkeer, en meer. Zie [beveiligings aanbevelingen beheren in azure Security Center](security-center-recommendations.md)voor een lijst met aanbevelingen en herstel acties voor netwerken.

> [!NOTE]
> Op de pagina **netwerken** kunt u de Azure-resource status van een netwerk perspectief dieper laten opkomen. De netwerk kaart en adaptieve netwerk besturings elementen zijn alleen beschikbaar voor de laag Azure Security Center standaard. [Als u de laag gratis gebruikt, kunt u klikken op de knop om **verouderde netwerken weer te geven** en aanbevelingen voor netwerk bronnen te ontvangen](#legacy-networking).
>

Op de pagina **netwerken** vindt u een overzicht van de secties die u uitgebreid kunt bekijken, voor meer informatie over de status van uw netwerk bronnen:

- Netwerk toewijzing (alleen Azure Security Center laag Standard)
- Adaptieve netwerkbeveiliging
- Aanbevelingen voor netwerk beveiliging.
- Blade verouderde **netwerken** (de Blade vorige netwerken) 
 
[![Netwerk venster](./media/security-center-network-recommendations/networking-pane.png)](./media/security-center-network-recommendations/networking-pane.png#lightbox)

## <a name="network-map"></a>Netwerk toewijzing
De interactieve netwerk kaart biedt een grafische weer gave met beveiligings-overlays waarmee u aanbevelingen en inzichten krijgt voor het versterken van uw netwerk bronnen. Met de kaart ziet u de netwerk topologie van uw Azure-workloads, verbindingen tussen uw virtuele machines en subnetten, en de mogelijkheid om in te zoomen op de kaart in specifieke bronnen en de aanbevelingen voor deze resources.

De netwerk kaart openen:

1. Selecteer in Security Center onder beveiligings hygiëne van de resource de optie **netwerken**.
2. Klik onder **netwerk kaart** op **topologie weer geven**.
 
De standaard weergave van de topologie kaart geeft het volgende weer:

- Abonnementen die u hebt geselecteerd in Azure. De kaart ondersteunt meerdere abonnementen.
- Vm's, subnetten en VNets van het Resource Manager-resource type (klassieke Azure-resources worden niet ondersteund)
- Peered VNets
- Alleen bronnen met [netwerk aanbevelingen](security-center-recommendations.md) met een hoge of gemiddelde Ernst  
- Internet gerichte bronnen
- De kaart is geoptimaliseerd voor de abonnementen die u hebt geselecteerd in Azure. Als u uw selectie wijzigt, wordt de kaart opnieuw berekend en opnieuw geoptimaliseerd op basis van de nieuwe instellingen.  

[![Netwerk topologie kaart](./media/security-center-network-recommendations/network-map-info.png)](./media/security-center-network-recommendations/network-map-info.png#lightbox)

## <a name="understanding-the-network-map"></a>Informatie over de netwerk kaart

De netwerk kaart kan uw Azure-resources weer geven in een **topologie** weergave en een **verkeers** weergave. 

### <a name="the-topology-view"></a>De topologie weergave

In de weer gave **topologie** van de netwerk kaart kunt u de volgende inzichten over uw netwerk bronnen bekijken:

- In de binnenste cirkel ziet u alle Vnets binnen de geselecteerde abonnementen, de volgende cirkel is alle subnetten, de buitenste cirkel is alle virtuele machines.
- Met de lijnen waarmee de resources in de kaart worden verbonden, kunt u zien welke resources aan elkaar zijn gekoppeld en hoe uw Azure-netwerk is gestructureerd. 
- Gebruik de ernst indicatoren om snel een overzicht te krijgen van de resources die de aanbevelingen van Security Center hebben geopend.
- U kunt klikken op een van de resources om in te zoomen en de details van die resource en de bijbehorende aanbevelingen direct te bekijken, en in de context van de netwerk kaart.  
- Als er te veel resources worden weer gegeven op de kaart, maakt Azure Security Center gebruik van het eigen algoritme voor het slim clusteren van uw resources, het markeren van de resources die de meest kritieke status hebben en hebben de meeste aanbevelingen voor de ernst van belang. 

Omdat de kaart interactief en dynamisch is, wordt elk knoop punt kan worden geklikt en kan de weer gave worden gewijzigd op basis van de filters:

1. U kunt wijzigen wat u ziet op de netwerk kaart door de filters bovenaan te gebruiken. U kunt de kaart richten op basis van:

   -  **Beveiligings status**: u kunt de kaart filteren op basis van de ernst (hoog, gemiddeld, laag) van uw Azure-resources.
   - **Aanbevelingen**: u kunt selecteren welke resources worden weer gegeven op basis waarvan de aanbevelingen actief zijn op deze resources. U kunt bijvoorbeeld alleen resources weer geven waarvoor Security Center raadt u aan om netwerk beveiligings groepen in te scha kelen.
   - **Netwerk zones**: standaard worden in de kaart alleen Internet gerichte bronnen weer gegeven. u kunt ook interne vm's selecteren.
 
2. U kunt op elk gewenst moment op **Reset** klikken in de linkerbovenhoek om de standaard status van de kaart te herstellen.

Inzoomen op een resource:

1. Wanneer u een specifieke resource op de kaart selecteert, wordt het rechterdeel venster geopend en krijgt u algemene informatie over de resource, verbonden beveiligings oplossingen, indien aanwezig, en de aanbevelingen die relevant zijn voor de resource. Dit is hetzelfde type gedrag voor elk type resource dat u selecteert. 
2. Wanneer u de muis aanwijzer boven een knoop punt in de kaart houdt, kunt u algemene informatie weer geven over de resource, inclusief het abonnement, het resource type en de resource groep.
3. Gebruik de koppeling om in te zoomen op de knop Info en richt de kaart op dat specifieke knoop punt. 
4. Uitzoomen om de kaart van een specifiek knoop punt te verplaatsen.

### <a name="the-traffic-view"></a>De verkeers weergave

De **verkeers** weergave biedt u een overzicht van alle mogelijke verkeer tussen uw resources. Hiermee krijgt u een visuele kaart van alle regels die u hebt geconfigureerd voor het definiëren van de resources waarmee kan worden gecommuniceerd. Zo kunt u de bestaande configuratie van de netwerk beveiligings groepen bekijken en snel mogelijke Risk ante configuraties in uw workloads identificeren.

### <a name="uncover-unwanted-connections"></a>Ongewenste verbindingen opsporen

De kracht van deze weer gave is in de mogelijkheid om deze toegestane verbindingen weer te geven met de beveiligings problemen die bestaan, zodat u deze Kruis sectie van gegevens kunt gebruiken om de benodigde beveiliging op uw resources uit te voeren. 

Zo kunt u bijvoorbeeld twee computers detecteren waarvan u niet weet dat ze kunnen communiceren, waardoor u de werk belastingen en subnetten beter kunt isoleren.

### <a name="investigate-resources"></a>Resources onderzoeken

Inzoomen op een resource:

1. Wanneer u een specifieke resource op de kaart selecteert, wordt het rechterdeel venster geopend en krijgt u algemene informatie over de resource, verbonden beveiligings oplossingen, indien aanwezig, en de aanbevelingen die relevant zijn voor de resource. Dit is hetzelfde type gedrag voor elk type resource dat u selecteert. 
2. Klik op **verkeer** om de lijst met mogelijk uitgaand en binnenkomend verkeer op de resource weer te geven. Dit is een uitgebreide lijst met personen die kunnen communiceren met de resource en waarmee kan worden gecommuniceerd en via welke protocollen en poorten. Wanneer u bijvoorbeeld een VM selecteert, worden alle virtuele machines die met de virtuele machine kunnen communiceren, weer gegeven en wanneer u een subnet selecteert, worden alle subnetten weer gegeven waarmee deze kan communiceren.

**Deze gegevens zijn gebaseerd op de analyse van de netwerk beveiligings groepen en geavanceerde machine learning algoritmen waarmee meerdere regels worden geanalyseerd om inzicht te krijgen in hun crossovers en interacties.** 

[![Netwerk verkeer toewijzing](./media/security-center-network-recommendations/network-map-traffic.png)](./media/security-center-network-recommendations/network-map-traffic.png#lightbox)


## <a name="legacy-networking"></a>Verouderde netwerken<a name ="legacy-networking"></a>

Als u geen Security Center Standard-laag hebt, wordt in deze sectie uitgelegd hoe u aanbevelingen voor gratis netwerken kunt weer geven.

Als u deze informatie wilt openen, klikt u op de Blade netwerken op **verouderde netwerken weer geven**. 

[![Verouderde netwerken](./media/security-center-network-recommendations/legacy-networking.png)](./media/security-center-network-recommendations/legacy-networking.png#lightbox)

### <a name="internet-facing-endpoints-section"></a>Sectie Internetgerichte eindpunten
In de sectie **Internet gerichte eind punten** kunt u de virtuele machines zien die momenteel zijn geconfigureerd met een Internet gericht eind punt en de status ervan.

Deze tabel bevat de naam van het eind punt, het Internet gerichte IP-adres en de huidige Ernst status van de netwerk beveiligings groep en de NGFW-aanbevelingen. De tabel is gesorteerd op Ernst.

### <a name="networking-topology-section"></a>Sectie Netwerktopologie
De sectie **netwerk topologie** bevat een hiërarchische weer gave van de resources.

Deze tabel is gesorteerd (virtuele machines en subnetten) op Ernst.

In deze topologie weergave wordt op het eerste niveau Vnets weer gegeven. De tweede bevat subnetten en het derde niveau geeft de virtuele machines weer die deel uitmaken van deze subnetten. In de rechter kolom ziet u de huidige status van de aanbevelingen voor de netwerk beveiligings groep voor deze resources.

Op het derde niveau worden virtuele machines weer gegeven. Dit is vergelijkbaar met wat eerder is beschreven. U kunt klikken op een resource voor meer informatie of de vereiste beveiligings controle of-configuratie Toep assen.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende voor meer informatie over de aanbevelingen die van toepassing zijn op andere Azure-resource typen:

* [Uw machines en toepassingen beveiligen in Azure Security Center](security-center-virtual-machine-protection.md)
* [Uw Azure SQL-service beveiligen in Azure Security Center](security-center-sql-service-recommendations.md)