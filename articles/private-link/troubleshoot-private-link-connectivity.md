---
title: Problemen met de connectiviteit van Azure Private Link oplossen
description: Stapsgewijze richtlijnen voor de diagnose van privékoppelingsconnectiviteit
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
ms.openlocfilehash: 1e5253d617c87d5869cebc817da6d265ebfdfa7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539464"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Problemen met de connectiviteit van Azure Private Link oplossen

In dit artikel vindt u stapsgewijze richtlijnen om de connectiviteit voor uw Azure Private Link-installatie te valideren en te diagnosticeren.

Met Azure Private Link hebt u toegang tot Azure-platform as a service (PaaS)-services, zoals Azure Storage, Azure Cosmos DB en Azure SQL Database, en Azure gehoste klant- of partnerservices via een privéeindpunt in uw virtuele netwerk. Verkeer tussen uw virtuele netwerk en de service loopt over het Microsoft-backbone-netwerk, waardoor de blootstelling van het openbare internet wordt geëlimineerd. U ook uw eigen privélinkservice in uw virtuele netwerk maken en privé aan uw klanten leveren.

U uw service inschakelen die achter de standaardlaag azure load balancer voor private link-toegang loopt. Consumenten van uw service kunnen een privéeindpunt maken binnen hun virtuele netwerk en het toewijzen aan deze service om er privé toegang toe te krijgen.

Dit zijn de verbindingsscenario's die beschikbaar zijn met Private Link:

- Virtueel netwerk uit dezelfde regio
- Regionaal gepeerde virtuele netwerken
- Wereldwijd gepeerde virtuele netwerken
- On-premises klant via VPN- of Azure ExpressRoute-circuits

## <a name="deployment-troubleshooting"></a>Problemen met implementatie

Bekijk de informatie over [Het uitschakelen van het netwerkbeleid op de privékoppelingsservice](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) voor probleemoplossingsaanvragen waarbij u het bron-IP-adres niet selecteren in het subnet van uw keuze voor uw privékoppelingsservice.

Controleer of de instelling **privateLinkServiceNetworkPolicies** is uitgeschakeld voor het subnet waaruit het bron-IP-adres wordt geselecteerd.

## <a name="diagnose-connectivity-problems"></a>Verbindingsproblemen diagnosticeren

Als u verbindingsproblemen ondervindt met het instellen van uw privékoppeling, controleert u deze stappen om ervoor te zorgen dat alle gebruikelijke configuraties zijn zoals verwacht.

1. Bekijk de configuratie van privékoppelingen door op de bron te bladeren.

    a. Ga naar **Private Link Center.**

      ![Privékoppelingscentrum](./media/private-link-tsg/private-link-center.png)

    b. Selecteer in het linkerdeelvenster De optie **Privékoppelingsservices**.

      ![Diensten voor privé-koppelingen](./media/private-link-tsg/private-link-service.png)

    c. Filter en selecteer de privékoppelingsservice die u wilt diagnosticeren.

    d. Bekijk de privé-eindpuntverbindingen.
     - Zorg ervoor dat het privéeindpunt waarvan u verbinding zoekt, wordt weergegeven met een **goedgekeurde** verbindingsstatus.
     - Als de status **in behandeling**is, selecteert u deze en keurt u deze goed.

       ![Privéeindpuntverbindingen](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Ga naar het privéeindpunt waarvan u verbinding maakt door de naam te selecteren. Controleer of de verbindingsstatus wordt weergegeven als **Goedgekeurd**.

       ![Overzicht van privéeindpuntverbindingen](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Nadat beide zijden zijn goedgekeurd, probeert u de verbinding opnieuw.

    e. Controleer **Alias** op het tabblad **Overzicht** en **Resource-id** op het tabblad **Eigenschappen.**
     - Controleer of de **gegevens alias** en **resource-id** overeenkomen met de **alias-** en **resource-id** die u gebruikt om een privéeindpunt voor deze service te maken.

       ![Aliasgegevens verifiëren](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Resource-id-gegevens verifiëren](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. **Bekijk zichtbaarheidsgegevens** op het tabblad **Overzicht.**
     - Zorg ervoor dat uw abonnement onder het **bereik van zichtbaarheid** valt.

       ![Zichtbaarheidsgegevens verifiëren](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g. Informatie **over de balansbalans van laden** controleren op het tabblad **Overzicht.**
     - U naar de load balancer gaan door de koppeling load balancer te selecteren.

       ![Gegevens van load balancer verifiëren](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Zorg ervoor dat de instellingen van de load balancer zijn geconfigureerd volgens uw verwachtingen.
       - Frontend **IP-configuratie bekijken**.
       - Bekijk **Backend-zwembaden**.
       - Regels **voor taakverdeling controleren**.

       ![Eigenschappen van load balancer controleren](./media/private-link-tsg/pls-ilb-properties.png)

     - Zorg ervoor dat de load balancer werkt volgens de vorige instellingen.
       - Selecteer een VM in een ander subnet dan het subnet waar de back-endpool van load balancer beschikbaar is.
       - Probeer toegang te krijgen tot de voorkant van de load balancer van de vorige vm.
       - Als de verbinding de back-endpool haalt volgens de regels voor het balanceren van de last, is uw load balancer operationeel.
       - U de load balancer-statistiek ook bekijken via Azure Monitor om te zien of er gegevens door de load balancer stromen.

1. Gebruik [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) om te zien of er gegevens stromen.

    a. Selecteer statistieken in de bron voor **privékoppelingsservice**.
     - Selecteer **Bytes in** of Bytes **out**.
     - Kijk of er gegevens stromen wanneer u verbinding probeert te maken met de privékoppelingsservice. Verwacht een vertraging van ongeveer 10 minuten.

       ![Statistieken voor privékoppelingenservice verifiëren](./media/private-link-tsg/pls-metrics.png)

1. Neem contact op met het [Azure Support-team](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) als uw probleem nog steeds is opgelost en er nog steeds een verbindingsprobleem bestaat.

## <a name="next-steps"></a>Volgende stappen

 * [Een private link-service (CLI) maken](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)
 * [Handleiding voor het oplossen van problemen met Azure Private Endpoint](troubleshoot-private-endpoint-connectivity.md)
