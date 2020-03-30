---
title: Uw netwerkbronnen beveiligen in Azure Security Center
description: Dit document richt aanbevelingen in Azure Security Center waarmee u uw Azure-netwerkbronnen beschermen en in overeenstemming blijft met het beveiligingsbeleid.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77431482"
---
# <a name="protect-your-network-resources"></a>Uw netwerkbronnen beveiligen
Azure Security Center analyseert continu de beveiligingsstatus van uw Azure-bronnen voor best practices voor netwerkbeveiliging. Wanneer Security Center potentiële beveiligingsproblemen identificeert, worden er aanbevelingen gemaakt die u begeleiden bij het configureren van de benodigde besturingselementen om uw resources te verharden en te beschermen.

In dit artikel wordt de **netwerkpagina** van het gedeelte resourcebeveiliging van Security Center uitgelegd.

Zie [Netwerkaanbevelingen](recommendations-reference.md#recs-network)voor een volledige lijst met de aanbevelingen voor netwerken.

In dit artikel worden aanbevelingen die van toepassing zijn op uw Azure-bronnen vanuit het oogpunt van netwerkbeveiliging, ingegaan. Netwerkaanbevelingen centreren rond firewalls van de volgende generatie, netwerkbeveiligingsgroepen, JIT VM-toegang, overdreven tolerante inkomende verkeersregels en meer. Zie [Beveiligingsaanbevelingen beheren in Azure Security Center](security-center-recommendations.md)voor een lijst met netwerkaanbevelingen en herstelacties.

> [!NOTE]
> Met **de netwerkpagina** u uw Azure-bronstatus diep induiken vanuit een netwerkperspectief. De netwerkkaart en adaptive network besturingselementen zijn alleen beschikbaar voor de standaardlaag Azure Security Center. [Als u de gratis laag gebruikt, u op de knop klikken om verouderde netwerken weer **te geven** en aanbevelingen voor netwerkbronnen te ontvangen.](#legacy-networking)
>

De **netwerkpagina** biedt een overzicht van de secties waar u diep in duiken, om meer informatie te krijgen over de gezondheid van uw netwerkbronnen:

- Netwerkkaart (alleen azure security centerstandaard)
- Adaptieve netwerkbeveiliging
- Waarschuwingen voor netwerkbeveiliging.
- Legacy **Networking** blade (het vorige netwerkblad) 
 
[![Netwerkvenster](./media/security-center-network-recommendations/networking-pane.png)](./media/security-center-network-recommendations/networking-pane.png#lightbox)

## <a name="network-map"></a>Netwerkkaart
De interactieve netwerkkaart biedt een grafische weergave met beveiligingsoverlays die u aanbevelingen en inzichten geven voor het verharden van uw netwerkbronnen. Met behulp van de kaart u de netwerktopologie van uw Azure-workloads, verbindingen tussen uw virtuele machines en subnetten en de mogelijkheid om vanaf de kaart in te zoomen in specifieke bronnen en de aanbevelingen voor die bronnen bekijken.

Ga als het gaat om het openen van de netwerkkaart:

1. Selecteer **Netwerken**in Security Center onder Resource Security Hygiene .
2. Klik **onder Netwerkkaart** op **Topologie weergeven**.
 
De standaardweergave van de topologiekaart wordt weergegeven:

- Abonnementen die u hebt geselecteerd in Azure. De kaart ondersteunt meerdere abonnementen.
- VM's, subnetten en VNets van het resourcetype Resource Manager (Klassieke Azure-bronnen worden niet ondersteund)
- Peered VNets
- Alleen resources met [netwerkaanbevelingen](security-center-recommendations.md) met een hoge of gemiddelde ernst  
- Internet geconfronteerd met bronnen
- De kaart is geoptimaliseerd voor de abonnementen die u in Azure hebt geselecteerd. Als u uw selectie wijzigt, wordt de kaart opnieuw berekend en opnieuw geoptimaliseerd op basis van uw nieuwe instellingen.  

[![Netwerktopologiekaart](./media/security-center-network-recommendations/network-map-info.png)](./media/security-center-network-recommendations/network-map-info.png#lightbox)

## <a name="understanding-the-network-map"></a>De netwerkkaart begrijpen

De netwerkkaart kan u uw Azure-bronnen weergeven in een **topologieweergave** en een **verkeersweergave.** 

### <a name="the-topology-view"></a>De topologieweergave

In de **topologieweergave** van de netwerkkaart u de volgende inzichten over uw netwerkbronnen bekijken:

- In de binnenste cirkel u alle Vnets binnen uw geselecteerde abonnementen zien, de volgende cirkel is alle subnetten, de buitenste cirkel is alle virtuele machines.
- De lijnen die de bronnen in de kaart met elkaar verbinden, laten u weten welke resources aan elkaar zijn gekoppeld en hoe uw Azure-netwerk is gestructureerd. 
- Gebruik de ernstindicatoren om snel een overzicht te krijgen van welke bronnen open aanbevelingen van Security Center hebben.
- U op een van de bronnen klikken om erin te zoomen en de details van die bron en de aanbevelingen rechtstreeks en in de context van de netwerkkaart te bekijken.  
- Als er te veel resources op de kaart worden weergegeven, gebruikt Azure Security Center het eigen algoritme om uw resources te slim te clusteren, de resources te markeren die zich in de meest kritieke status bevinden en de meest krachtige aanbevelingen hebben. 

Omdat de kaart interactief en dynamisch is, is elk knooppunt klikbaar en kan de weergave veranderen op basis van de filters:

1. U wijzigen wat u op de netwerkkaart ziet met behulp van de filters bovenaan. U de kaart scherpstellen op basis van:

   -  **Beveiligingsstatus:** u de kaart filteren op basis van ernst (hoog, gemiddeld, laag) van uw Azure-resources.
   - **Aanbevelingen:** U selecteren welke resources worden weergegeven op basis van welke aanbevelingen actief zijn op deze bronnen. U bijvoorbeeld alleen resources weergeven waarvoor u aanraadt netwerkbeveiligingsgroepen in te schakelen.
   - **Netwerkzones**: Standaard wordt op de kaart alleen internetgerichte bronnen weergegeven, u ook interne VM's selecteren.
 
2. U op elk gewenst moment linksboven op **Opnieuw** instellen klikken om de kaart terug te brengen naar de standaardstatus.

Ga als lid van de bron naar beneden:

1. Wanneer u een specifieke resource op de kaart selecteert, wordt het rechterdeelvenster geopend en krijgt u algemene informatie over de resource, verbonden beveiligingsoplossingen als die er zijn en de aanbevelingen die relevant zijn voor de resource. Het is hetzelfde type gedrag voor elk type resource dat u selecteert. 
2. Wanneer u de boventoon over een knooppunt in de kaart zet, u algemene informatie over de resource weergeven, waaronder abonnement, resourcetype en resourcegroep.
3. Gebruik de koppeling om in te zoomen op de knopinfo en de kaart opnieuw scherp te stellen op dat specifieke knooppunt. 
4. Als u de kaart opnieuw wilt richten op een specifiek knooppunt, zoomt u uit.

### <a name="the-traffic-view"></a>De weergave Verkeer

De **verkeersweergave** biedt u een kaart van al het mogelijke verkeer tussen uw resources. Dit biedt u een visuele kaart van alle regels die u hebt geconfigureerd die bepalen welke resources met wie kunnen communiceren. Hierdoor u de bestaande configuratie van de netwerkbeveiligingsgroepen zien en snel mogelijke risicovolle configuraties binnen uw workloads identificeren.

### <a name="uncover-unwanted-connections"></a>Ongewenste verbindingen ontdekken

De kracht van deze weergave is in zijn vermogen om u te laten zien deze toegestane verbindingen samen met de kwetsbaarheden die er zijn, zodat u deze dwarsdoorsnede van gegevens gebruiken om de nodige verharding uit te voeren op uw resources. 

U bijvoorbeeld detecteren dat twee machines waarvan u niet wist dat ze kunnen communiceren, zodat u de workloads en subnetten beter isoleren.

### <a name="investigate-resources"></a>Resources onderzoeken

Ga als lid van de bron naar beneden:

1. Wanneer u een specifieke resource op de kaart selecteert, wordt het rechterdeelvenster geopend en krijgt u algemene informatie over de resource, verbonden beveiligingsoplossingen als die er zijn en de aanbevelingen die relevant zijn voor de resource. Het is hetzelfde type gedrag voor elk type resource dat u selecteert. 
2. Klik **op Verkeer** om de lijst met mogelijk uitgaand en binnenkomend verkeer op de resource te bekijken - dit is een uitgebreide lijst van wie met de resource kan communiceren en met wie deze kan communiceren en via welke protocollen en poorten. Wanneer u bijvoorbeeld een VM selecteert, worden alle VM's waarmee deze kan communiceren weergegeven en wanneer u een subnet selecteert, worden alle subnetten waarmee deze kan communiceren weergegeven.

**Deze gegevens zijn gebaseerd op analyse van de Netwerkbeveiligingsgroepen en geavanceerde machine learning-algoritmen die meerdere regels analyseren om hun cross-overs en interacties te begrijpen.** 

[![Netwerkverkeerskaart](./media/security-center-network-recommendations/network-map-traffic.png)](./media/security-center-network-recommendations/network-map-traffic.png#lightbox)


## <a name="legacy-networking"></a>Verouderde netwerken<a name ="legacy-networking"></a>

Als u geen Standard-laag voor beveiligingscentrum hebt, wordt in dit gedeelte uitgelegd hoe u aanbevelingen voor gratis netwerken weergeven.

Als u toegang wilt krijgen tot deze informatie, klikt u in het netwerkblad op **Oudere netwerken weergeven**. 

[![Legacy-netwerken](./media/security-center-network-recommendations/legacy-networking.png)](./media/security-center-network-recommendations/legacy-networking.png#lightbox)

### <a name="internet-facing-endpoints-section"></a>Sectie Internetgerichte eindpunten
In de sectie **Eindpunten voor internetgeconfronteerden** ziet u de virtuele machines die momenteel zijn geconfigureerd met een eindpunt voor interneten en de status ervan.

Deze tabel heeft de naam van het eindpunt, het IP-adres voor internet en de huidige ernststatus van de netwerkbeveiligingsgroep en de NGFW-aanbevelingen. De tabel wordt gesorteerd op ernst.

### <a name="networking-topology-section"></a>Sectie Netwerktopologie
De sectie **Netwerktopologie** heeft een hiërarchische weergave van de resources.

Deze tabel wordt gesorteerd (virtuele machines en subnetten) op ernst.

In deze topologieweergave wordt vnets op het eerste niveau weergegeven. De tweede toont subnetten, en het derde niveau toont de virtuele machines die behoren tot die subnetten. In de rechterkolom wordt de huidige status van de aanbevelingen van de netwerkbeveiligingsgroep voor deze resources weergegeven.

Het derde niveau toont virtuele machines, die vergelijkbaar is met wat eerder wordt beschreven. U op een resource klikken om meer te weten te komen of de vereiste beveiligingscontrole of configuratie toe te passen.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende voor meer informatie over aanbevelingen die van toepassing zijn op andere Azure-brontypen:

* [Uw machines en toepassingen beveiligen in Azure Security Center](security-center-virtual-machine-protection.md)
* [Uw Azure SQL-service beveiligen in Azure Security Center](security-center-sql-service-recommendations.md)