---
title: Connectiviteits problemen met Azure-particuliere koppelingen oplossen
description: Stapsgewijze instructies voor het vaststellen van de connectiviteit van particuliere koppelingen
services: private-link
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 667fa1c85c63ffb87e49c4bf99112f57d0c85a72
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031918"
---
# <a name="troubleshoot-private-link-service-connectivity-problems"></a>Verbindings problemen met de persoonlijke koppelings service oplossen

Deze hand leiding bevat stapsgewijze instructies voor het valideren en diagnosticeren van de connectiviteit voor de installatie van uw persoonlijke koppelings service. 

Met Azure private link kunt u toegang krijgen tot Azure PaaS-Services (bijvoorbeeld Azure Storage, Azure Cosmos DB en SQL Database) en Azure hostende klanten/partner services via een persoonlijk eind punt in uw virtuele netwerk. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U kunt ook uw eigen privé koppelings service maken in uw virtuele netwerk (VNet) en deze privé leveren aan uw klanten. 

U kunt de service die wordt uitgevoerd achter Azure Standard Load Balancer inschakelen voor toegang tot persoonlijke koppelingen. Consumenten van uw service kunnen een persoonlijk eind punt in hun virtuele netwerk maken en dit koppelen aan deze service om het privé te benaderen.

Hier vindt u de verbindings scenario's die beschikbaar zijn voor de service private link
- virtueel netwerk uit dezelfde regio 
- regionaal peered virtuele netwerken
- wereld wijd gekoppelde virtuele netwerken
- on-premises klant via VPN-of Express route-circuits

## <a name="deployment-troubleshooting"></a>Problemen met implementatie oplossen

Lees de informatie over het [uitschakelen van netwerk beleid op de privé koppelings service](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) voor het oplossen van problemen waarbij u het bron-IP-adres niet kunt selecteren in het subnet van uw keuze voor uw persoonlijke koppelings service.

Zorg ervoor dat de instelling **privateLinkServiceNetworkPolicies** is uitgeschakeld voor het subnet waarvoor u het bron-IP-adres selecteert.

## <a name="diagnosing-connectivity-problems"></a>Verbindings problemen vaststellen

Als u verbindings problemen ondervindt met het instellen van uw persoonlijke koppeling, gaat u naar de onderstaande stappen om ervoor te zorgen dat alle gebruikelijke configuraties naar verwachting worden uitgevoerd.

1. Controleer de configuratie van de service voor persoonlijke koppelingen door te bladeren door de resource 

    a) Ga naar het **persoonlijke koppelings centrum**

      ![Persoonlijk koppelings centrum](./media/private-link-tsg/private-link-center.png)

    b) Selecteer **persoonlijke koppelings Services** in het navigatie deel venster links

      ![Services voor persoonlijke koppelingen](./media/private-link-tsg/private-link-service.png)

    c) filter en selecteer de persoonlijke koppelings service die u wilt diagnosticeren

    d) de verbindingen met het privé-eind punt controleren
     - Zorg ervoor dat het persoonlijke eind punt waarvan u verbinding zoekt, wordt weer gegeven met de status **goedgekeurd** verbinding. 
     - Als de bewerking **in behandeling**is, selecteert u deze en keurt u deze goed. 

       ![Verbindingen met privé-eind punten](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Ga naar het persoonlijke eind punt waarmee u verbinding maakt door te klikken op de naam. Zorg ervoor dat de status van de verbinding **goed**wordt weer gegeven.

       ![Overzicht van de verbinding met het persoonlijke eind punt](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Zodra beide zijden zijn goedgekeurd, kunt u de connectiviteit opnieuw proberen.

    e) de **alias** van het tabblad Overzicht en de **resource-id** op het tabblad Eigenschappen controleren 
     - Zorg ervoor dat de **alias/resource-id** overeenkomt met de **alias/resource-id** die u gebruikt om een persoonlijk eind punt te maken voor deze service. 

       ![Alias controleren](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Resource-ID verifiëren](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f) zicht baarheid van de beoordeling (sectie Overzicht)
     - Zorg ervoor dat uw abonnement onder het **zichtbaarheids** bereik valt

       ![Zicht baarheid controleren](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g) Lees Load Balancer (overzicht) informatie
     - U kunt navigeren naar load balancer door te klikken op load balancer koppeling

       ![Load Balancer controleren](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Zorg ervoor dat Load Balancer instellingen zijn geconfigureerd volgens uw verwachtingen
       - Frontend-IP-configuratie controleren
       - Back-end-Pools controleren
       - Taakverdelings regels bekijken

       ![Load Balancer eigenschappen controleren](./media/private-link-tsg/pls-ilb-properties.png)

     - Zorg ervoor dat Load Balancer volgens bovenstaande instellingen werkt
       - Selecteer een virtuele machine in een ander subnet dan het subnet waar Load Balancer back-end-pool beschikbaar is
       - Probeer de load balancer front-end van de virtuele machine te openen
       - Als de verbinding maakt met de back-end-groep volgens de regels voor taak verdeling, is uw load balancer operationeel
       - U kunt ook de Load Balancer metriek door Azure Monitor bekijken om te zien of gegevens stromen via load balancer

2. [**Azure monitor**](https://docs.microsoft.com/azure/azure-monitor/overview) gebruiken om gegevens te controleren, loopt stroom

    a) op de SErvice resource van de persoonlijke koppeling selecteert u **metrische gegevens**
     - **Bytes-in** -of **-byte-out** selecteren
     - De gegevens worden gestroomd wanneer wordt geprobeerd verbinding te maken met de persoonlijke koppelings service. Er wordt een vertraging van ongeveer 10 minuten verwacht.

       ![De metrische gegevens van de service voor persoonlijke koppelingen controleren](./media/private-link-tsg/pls-metrics.png)

3. Neem contact op met het [ondersteunings](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) team van Azure als uw probleem nog steeds niet is opgelost en er nog steeds een verbindings probleem is 

## <a name="next-steps"></a>Volgende stappen

 * [Een service voor persoonlijke koppelingen maken](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)

 * [Gids voor probleem oplossing voor privé-eind punten](https://docs.microsoft.com/azure/private-link/private-endpoint-connectivity-troubleshooting)
