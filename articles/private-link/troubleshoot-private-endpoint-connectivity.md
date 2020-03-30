---
title: Verbindingsproblemen met het Azure-privé-eindpunt oplossen
description: Stapsgewijze richtlijnen voor het diagnosticeren van privé-eindpuntconnectiviteit
services: private-endpoint
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
ms.openlocfilehash: fcc482e6231bbd925fd500a37989052765dede58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538531"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Verbindingsproblemen met het Azure-privé-eindpunt oplossen

In dit artikel vindt u stapsgewijze richtlijnen om de installatie van uw Azure Private Endpoint-connectiviteit te valideren en te diagnosticeren.

Azure Private Endpoint is een netwerkinterface die u privé en veilig verbindt met een private link-service. Met deze oplossing u uw workloads in Azure beveiligen door privéconnectiviteit te bieden met uw Azure-servicebronnen vanuit uw virtuele netwerk. Deze oplossing brengt deze diensten effectief naar uw virtuele netwerk.

Dit zijn de verbindingsscenario's die beschikbaar zijn met Privéeindpunt:

- Virtueel netwerk uit dezelfde regio
- Regionaal gepeerde virtuele netwerken
- Wereldwijd gepeerde virtuele netwerken
- On-premises klant via VPN- of Azure ExpressRoute-circuits

## <a name="diagnose-connectivity-problems"></a>Verbindingsproblemen diagnosticeren 

Bekijk deze stappen om ervoor te zorgen dat alle gebruikelijke configuraties zijn zoals verwacht om verbindingsproblemen met uw privé-eindpuntinstelling op te lossen.

1. Bekijk de configuratie van privéeindpunten door op de bron te bladeren.

    a. Ga naar **Private Link Center.**

      ![Privékoppelingscentrum](./media/private-endpoint-tsg/private-link-center.png)

    b. Selecteer **privéeindpunten**in het linkerdeelvenster .
    
      ![Privéeindpunten](./media/private-endpoint-tsg/private-endpoints.png)

    c. Filter en selecteer het privéeindpunt dat u wilt diagnosticeren.

    d. Bekijk de virtuele netwerk- en DNS-informatie.
     - Valideren of de verbindingsstatus is **goedgekeurd**.
     - Zorg ervoor dat de VM verbinding heeft met het virtuele netwerk dat de privéeindpunten host.
     - Controleer of de FQDN-informatie (kopie) en privé-IP-adres zijn toegewezen.
    
       ![Virtuele netwerk- en DNS-configuratie](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. Gebruik [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) om te zien of er gegevens stromen.

    a. Selecteer **Monitor**in de privéeindpuntbron .
     - Selecteer **Gegevens in** of Gegevens **uit**. 
     - Kijk of er gegevens stromen wanneer u verbinding probeert te maken met het privéeindpunt. Verwacht een vertraging van ongeveer 10 minuten.
    
       ![Privéeindpunttelemetrie verifiëren](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Probleemproblemen met **VM-verbinding gebruiken** vanuit Azure Network Watcher.

    a. Selecteer de client-VM.

    b. Selecteer **Problemen met verbinding**en selecteer vervolgens het tabblad Uitgaande **verbindingen.**
    
      ![Network Watcher - Uitgaande verbindingen testen](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. Selecteer **Netwerkwatcher gebruiken voor gedetailleerde verbindingstracering**.
    
      ![Network Watcher - Problemen met verbinding](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. Selecteer **Testen op FQDN**.
     - Plak de FQDN uit de bron voor privéeindpunten.
     - Zorg voor een poort. Gebruik doorgaans 443 voor Azure Storage of Azure Cosmos DB en 1336 voor SQL.

    e. Selecteer **Testen**en valideer de testresultaten.
    
      ![Network Watcher - Testresultaten](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. DNS-resolutie van de testresultaten moet hetzelfde privé-IP-adres hebben dat is toegewezen aan het privéeindpunt.

    a. Als de DNS-instellingen onjuist zijn, voert u de volgende stappen uit:
     - Als u een privézone gebruikt: 
       - Zorg ervoor dat het virtuele client-VM-netwerk is gekoppeld aan de privézone.
       - Controleer of de record van de privé-DNS-zone bestaat. Als het niet bestaat, maak het dan.
     - Als u aangepaste DNS gebruikt:
       - Controleer uw aangepaste DNS-instellingen en controleer of de DNS-configuratie juist is.
       Zie Overzicht [privéeindpunt voor richtlijnen: DNS-configuratie](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration).

    b. Als de connectiviteit mislukt vanwege netwerkbeveiligingsgroepen (NSG's) of door de gebruiker gedefinieerde routes:
     - Bekijk de NSG-outbound-regels en maak de juiste uitgaande regels om verkeer toe te staan.
    
       ![NSG-uitgaande regels](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. Als de verbinding gevalideerde resultaten heeft, kan het verbindingsprobleem verband houden met andere aspecten zoals geheimen, tokens en wachtwoorden op de toepassingslaag.
   - Controleer in dit geval de configuratie van de private link-bron die is gekoppeld aan het privéeindpunt. Zie de handleiding [voor probleemoplossing voor Azure Private Link](troubleshoot-private-link-connectivity.md)voor meer informatie.

1. Neem contact op met het [Azure Support-team](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) als uw probleem nog steeds is opgelost en er nog steeds een verbindingsprobleem bestaat.

## <a name="next-steps"></a>Volgende stappen

 * [Een privéeindpunt maken op het bijgewerkte subnet (Azure-portal)](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)
 * [Handleiding voor probleemoplossing azure Private Link](troubleshoot-private-link-connectivity.md)
